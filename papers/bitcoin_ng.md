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
- Introduces the first quantatative metrics for performance evaluation of Bitcoin.
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

