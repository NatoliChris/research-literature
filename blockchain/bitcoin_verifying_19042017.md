# How bitcoin verifies

## Citations:

- [https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages)
- [https://en.bitcoin.it/wiki/Protocol_rules#.22block.22_messages](https://en.bitcoin.it/wiki/Protocol_rules#.22block.22_messages)

## Verifying Transactions:

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
- Using refernced output transactions to get input values - check each input as well as sum are in legal range.
- Reject if sum of input less than sum of output.
- Reject if transaction fee (sum of input minus sum of output) would be too low for empty block.
- Verify the ``scriptPubKey`` accepts for each input - reject if bad.
- Add to transaction pool.
	- *NOTE:* when added to pool, additional check ensured that coinbase does not exceed transaction fee + expected BTC value.
- If transaction is mine then add to wallet.
- Relay transaction to peers.

