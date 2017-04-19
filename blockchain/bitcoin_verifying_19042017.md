# How bitcoin verifies

## Citations:

- [https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages)
- [https://en.bitcoin.it/wiki/Protocol_rules#.22block.22_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22block.22_messages)

## Verifying Transactions:

### Transaction Collections:

- **Transaction Pool**: unordered collection of transactions not in main chain but have input transactions that exist.
- **Orphan Transactions**: transactions that can't go into the pool due to one or more missing input transactions.

### Verifying TX Messages: 

- Check that has correct syntax.
- Make sure no *IN* or *OUT* is empty.
- Correct Size. (Less or Equal to ``MAX_BLOCK_SIZE``)
- Each output as well as total must be legal range.
- None of the inputs have ``hash = 0 or n = -1`` ( *coinbase* transactions )
- Check that:
	- ``nLockTime <= INT_MAX`` (31 bits).
	- Size in bytes ``>= 100`` (100 bytes is minimum transaction size).
	- ``sig opcount <= 2`` (Number of signature operands - never exceeds two in tx).
- Reject *non-standard* transactions (``sigScript`` doing nothing but pushing numbers OR ``scriptPubKey`` not matching forms.
	- Flexible on clients
- Reject if matching tx in pool/block in main branch.
- For each input:
	- Look in main branch and txpool to find referenced output transaction.
	- if tx output is missing for input - orphan transaction - add to orphan pool if not already.
	- if referenced output is *coinbase* ( ``h=0, n=-1`` ), then it must have ``COINBASE_MATURITY(100)`` confirmations.
	- if referenced output does not exist (never existed OR spent) then reject.
- Using referenced output transactions to get input values - check each input as well as sum are in legal range.
- Reject if sum of input less than sum of output.
- Reject if transaction fee (sum of input minus sum of output) would be too low for empty block.
- Verify the ``scriptPubKey`` accepts for each input - reject if bad.
- Add to transaction pool.
	- *NOTE:* when added to pool, additional check ensured that coinbase does not exceed transaction fee + expected BTC value.
- If transaction is mine then add to wallet.
- Relay transaction to peers.

## Block Messages:

### Block categories:

- **Blocks in Main Branch**: transactions in blocks considered at least tentatively confirmed.
- **Blocks on side branches off the main branch**: blocks tentatively lost the race to be in main branch.
- **Orphan Blocks**: blocks don't link into main branch, normally because missing predecessor or nth-level predecessor.

### Verifying Block messages:

- Check syntax correct.
- Reject if there is a duplicate block (in either category).
- Check transaction list not empty.
- Block hash must satisfy the claimed ``nBits`` proof-of-work.
- Block timestamp MUST be within 2 hours of future.
- First transaction must be ``coinbase`` where ``length(input) = 1; hash=0; n=-1``
- For each transaction:
	- Apply check on:
		- size
		- in/out not empty
		- each output and total in legal money range
- For coinbase transaction:
	- ``sigScript`` length must be 2-100
- Reject if sum of transaction ``sig opcounts > MAX_BLOCK_SIGOPS``.
- Verify merkle hash
- Check if prev block (matching prev hash) is in main branch or side branch.
	- If not then add to orphan branch.
	- Query peer that gave orphan block for the missing information.
- Check that ``nBits`` matches difficulty.
- Reject if timestamp is the median time of the last 11 blocks or more.
- For certain old blocks check hash matches known values. (on initial download of block)
- Add block into tree, three cases:
	1. Block further extends the main branch
	2. Block extends a side branch but does not add enough difficulty to make it become the new branch.
	3. Block extends the side branch and the difficulty makes it the new branch.
- For each orphan block for which this block is its ``prev`` - run all steps recursively on the orphan.

### Dealing with the Cases:

#### Case 1 : further extension of main

- For all but the coinbase transaction:
	- For each input 
		- look into main branch to find referenced transaction (reject if the output of referenced transaction is missing).
		- if we are using the ``nth`` output of the earlier transaction, but it has fewer than ``n+1`` outputs, then reject.
		- If the referenced output transaction is coinbase ``hash = 0; n = 1; input length is 1`` then must have ``COINBASE_MATURITY(100)`` confirmations; else reject.
		- Verify the crypto (signatures) for each input. Reject if bad.
		- If referenced output has already been spent by transaction in main branch - reject.
	- Using the referenced output transactions to get input values - check that each input value, as well as sum, are legal money range.
	- Reject if sum of input less than sum of output.
- Reject if coinbase value greater than sum of block creation and transaction fees.
- (If we haven't rejected):
	- For each transaction:
		- Add to wallet if mine.
		- Delete any matching transaction from transaction pool.
		- Relay block to peers. 
- If we rejected block, block not counted as part of the main branch.

#### Case 2 : Adding to side branch - doesn't make the branch become main branch.

- Add to side branch
- Don't do anything.

#### Case 3 : Add to side branch - makes this branch main branch.

- Find the ``fork`` block on the main branch which this side branch forks from.
- Redefine the main branch to only go up to this forked block.
- For each block in side branch, from the child of the forked block to the leaf - add to main branch:
	- Do the ``branch`` checks
	- For all but coinbase:
		- For each input:
			- look in main branch for referenced output. Reject if missing.
			- if we are using the ``nth`` output of earlier transactions but it has fewer than ``n+1`` outputs, then reject.
			- If the referenced output transaction is coinbase ``hash = 0; n = 1; input length is 1`` then must have ``COINBASE_MATURITY(100)`` confirmations; else reject.
			- Verify the crypto (signatures) for each input. Reject if bad.
			- Check to see if referenced output has already been spent by a transaction in main branch.
			- Using the referenced output transactions to get input values, check each input value as well as sum is in legal money range.
			- Reject if sum of input less than sum of output.
	- Reject if coinbase value greater than sum of block creation and transaction fees.
	- If not rejected
		- Add to wallet if mine.
- If we reject at any point, leave main branch as what it originally was before.
- For each block in old main branch - from leaf own to child of fork block:
	- For each non-coinbase transaction:
		- Apply ``tx`` checks - look in transaction pool for duplicates not main branch (since we are re-organising the chain).
		- Add to the transaction pool if it is accepted, else move on.
- For each block in the new main branch - from child of fork to leaf.
	- For each transaction in the block, delete any matching transaction from the transaction pool.
- Relay block to peers.
