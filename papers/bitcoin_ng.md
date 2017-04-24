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
