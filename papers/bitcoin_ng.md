# Bitcoin-NG (Bitcoin-Next Generation)

Citation:

```
@inproceedings{eyal2016bitcoin,
  title={Bitcoin-ng: A scalable blockchain protocol},
  author={Eyal, Ittay and Gencer, Adem Efe and Sirer, Emin Gun and Van Renesse, Robbert}
}

```

---

## Summary

### Motivation:

- Cryptocurrencies show promising benefits for infrastructure.
- Bitcoin is leading currently, but needs to be improved.

### Problem:

- Bitcoin derived blockchains face issues of scalability and throughput.
- Performance is based off two things:
	- **Block Size**:
		- Bigger size is better throughput.
		- Longer to propagate in the network.
	- **Block Interval**
		- Reduces latency with more blocks per time.
		- Leads to more disagreement.

### Contribution:

- Introduces the Bitcoin-NG protocol.
	- Improves bitcoin performance while keeping same assumptions.
	- ``//TODO discuss more about protocol``
- Introduces the first quantitative metrics for performance evaluation of Bitcoin.
- Quantifies scalability and performance of the Bitcoin-NG protocol through large-scale experiments.

---

## Introduction

* Bitcoin and cryptocurrencies have gained high attention.
* Core of most mainstream blockchains is *Nakamoto Consensus*.
* Ongoing work tries to expand the Bitcoin's functionality and performance.
* Bitcoin faces scalability problems.
* Max rate based on:
	* Block size.
	* Block interval.
* Bitcoin-NG scalable protocol limited only by network delay and processing capacity of nodes.
* Improvement decouples the Bitcoin blockchain by:
	* leader election.
	* transaction serialization.

## Model and Goal

- System:
	- Set of nodes in a *reliable* peer-to-peer network.
	- Each node can poll a random oracle.
	- Nodes generate key pairs but no trusted pubkey infrastructure.
- System cryptopuzzle system:
	- Each node has a limited amount of computing power (mining power)
	- Solution to a puzzle shows proof-of-work, indicates that node spent time to find the solution.
- Any time, subset of nodes are *Byzantine*, the other nodes are honest.
- The mining power of the Byzantine nodes is less than 1/4 of the total computing power of the network.

### Nakamoto Consensus

- Termination:
	- Small probability that the same node will depict two different system states for time ``t``, at time ``t`` and ``t + delta``.
- Agreement:
	- Small probability that two nodes return different states for a time ``t-delta`` at time ``t``.
- Validity:
	- Average fraction of state machine transitions that are not inputs of honest nodes is smaller than f.

## Bitcoin and Blockchain Protocol

- Replicated state machine maintains balances of different users, transitions are the transactions that move funds.
- A transaction is from the output of a previous transaction to an address.
- An output is *spent* if it is the input of another transaction.
- Miners accept transactions only if their sources have not been spent.
- The miners commit the transaction onto a global *append-only* log, the **blockchain**.
- The blockchain records transactions in units of blocks:
	- Each block has a unique ID.
	- The first block is the *genesis block*.
	- A valid block contains:
		- Solution to cryptopuzzle.
		- The blcok's hash.
		- A special *coinbase* transaction rewarding the miner.

## Bitcoin-NG

### Key blocks and Leader Election

- Key blocks choose a leader, contains:
	- reference to prev. block.
	- current Unix time.
	- coinbase transaction for reward.
	- target value.
	- nonce field.
	- **public key** to be used in subsequent microblocks.
- Difficulty is adjusted by deterministically changing target value based on Unix value in headers.
- If fork - nodes choose heaviest chain (most work).

### Microblocks

- Leader generates microblocks at set rate.
- Size bounded by predefined maximum.
- If timestamp of microblock is in future ,microblock invalid.
- Microblocks contain:
	- Transactions (ledger entries).
	- header:
		- reference to prev. block.
		- current Unix time.
		- cryptographic hash of ledger entries.
		- Crypto signature of header. (signed with priv. key matching pub of leader).
- Microblocks do not affect weight of chain as they don't have any proof-of-work.

### Confirmation Time

- Microblock fork when blocks propagated after leader change.
- Resolved once the new key block is received by node.
	- Seeing a microblock should wait propagation time before checking which branch is main.

### Remuneration

- Leader compensated for mining.
- Remuneration:
	- Key block = set amount.
	- Each transaction has fee.
		- Current leader = 40% of fee.
		- Subsequent leader = 60% of fee.
- Can only be spent after 100 block maturity.

### Microblock Fork Prevention

- Don't require mining - as cheap/quick as the leader is allowed.
- To demotivate fraud/double spending, dedicataed ledger entry that invalidates any revenue from malicious leaders.
- **Poison Transaction**: header of first block in fork as *proof-of-fraud*.
