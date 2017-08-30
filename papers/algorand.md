# Algorand: Scaling Byzantine Agreements for Cryptocurrencies.

Yossi Gilad, Rotem Hemo, Silvio Micali, Georgios Vlachos, Nikolai Zeldovich.

```
@inproceedings{gilad2017algorand,
  title={Algorand: Scaling Byzantine Agreements for Cryptocurrencies},
  author={Gilad, Yossi and Hemo, Rotem and Micali, Silvio and Vlachos, Georgios and Zeldovich, Nickolai},
  year={2017},
  organization={SOSP}
}
```

---

## Summary

### Motivation

* Blockchains are too slow to be practical.
* Guarantees of blockchain are strong, current model is not.


### Problem

* Proof-of-work is too slow and costly.
* Current consensus can *fork*.

### Contribution

* Algorand blockchain,**proof-of-stake** using new Byzantine Agreement consensus.
* Fast throughput (30x bitcoin's) with scalability.
* Random selection of consensus group making it safer than current fixed models.

---

## Introduction

* Cryptocurrencies offer many good things.
    * Smart contracts.
    * Fair protocols.
    * Simple currency conversion.
    * No need for centralized trusted third party.
* Achieving confidence takes a long time (1h on Bitcoin).
* Proof-of-Work provides resilience against sybil attacks and byzantine nodes, but suffers from forks.
* Algorand:
    * Utilises **BA-Star** algorithm for consensus.
    * **Weighted Users**: each user has a weight assigned to achieve honest users as majority.
        * Prevents Sybil Attacks
    * **Consensus by Committee**: BA-star algorithm achieves scalability by using traditional mechanisms of a small subset of nodes.
        * Allows scalability.
    * **Cryptographic Sortition**: preventing adversary from targeting committee members. Uses the **Verifiable Random Function** (VRF) to determine if they are part of the chosen committee.
    * **Partitipant Replacement**: Since adversary can target committee members, new committee members chosen for each step in BA-star.

## Related Work

* Proof-of-work from Nakamoto
    * Is slow and can have forks.
    * Algorand eliminates the possibility of forks using the BA-star algorithm.
* Byzantine Consensus:
    * PBFT, BFT2F have been developed but require a fixed set of participants. If dynamic then would allow for Sybil Attacks.
    * BA-star is designed to scale, and provides the algorithm so that the chain will never fork.
    * HoneyBadgerBFT demonstrates the use of the byzantine agreement algorithms in a cryptocurrency environment.
* Proof-of-Stake
    * Algorand assigns weight based on monetary value.
    * Traditional proof-of-stake allows a malicious leader to publish blocks and create forks.
    * Algorand's proof-of-stake ensures that the attacker cannot amplify it's power.
        * Going to be extended to punish bad leaders.
* Tree's and DAGs instead of chains
    * GHOST/SPECTRE proposals for Bitcoin to deal with chains. 
    * Careful design of the selection rule between branches of a DAG, algorand utilises a tree / DAG to increase throughput.


## Goals and Assumptions

* **Safety**: all users agree on the same transaction, the network is always in a consistent state.
    * Algorand guarantees both latency and high-confidence in transaction confirmation.
    * To ensure safety, Algorand makes no assumption of the network; attacker can have full control. 
* **Liveness**: Algorand aims to reach consensus on a new set of transactions in under a minute.
    * To ensure liveness, Algorand requires that more than a threshold of honest users can communicate with each other.
* Belief that a fraction of money held by honest users is above a threshold (greater than 2/3). 

