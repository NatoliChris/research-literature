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

## Overview

* Algorand communicates through a gossip protocol used by users to submit transactions and receives a block of pending transactions that they hear about.
* BA-star executes in steps, communicates over the same gossip protocol and produces a new agreed-upon block.
* **Gossip Protocol**:
    * Gossip network similar to Bitcoin/Ethereum (P2P Network) where each user gossips a messages to a chosen set of peers.
    * To ensure messages can't be forged, every message is signed.
    * To avoid loops, users do not relay the same message twice.
    * Peers are **weighed** based on monetary value.
* **Block Proposal**:
    * Each suer executes cryptographic sortition to determine whether they are to propose a block.
    * Users selected at random, weighted based on monetary value. Each user has a *priority* which can be compared against users.
    * Blocks are prioritised based on the proposing user's priority.
* **Agreement with BA-star**:
    * Does not guarantee that all users received the same block.
    * Algorand uses BA-star to agree on a block - each node broadcasts a message includes proof-of-selection.
    * Committee members do not keep private state so can be replaced after every step. (Used to mitigate attacks on them).
* **Efficiency**:
    * BA-star guarantees that if all honest users start with the same initial block, then BA-star establishes consensus over the block and terminates in 3 steps.
    * Worst-case = all honest users reach consensus on the next block.

## Cryptographic Sortition

* Algorithm for choosing a randoms subset of users according to per-user weights.
* The randomness comes from a publicly known *seed*.
* Implemented using the (Verifiable Random Function) VRF.
* The cryptographic hash is only provided with the user's private key (determining if they had been chosen).

### Choosing Seed

* Algorand: Each round requires a new seed.
* Seed determined using the VRF with the previous round's seed.
* Private key is chosen by user well in advance of the seed of previous round being determined.
* Seed included (with proof) in every block proposed. If block doesn't contain valid seed users treat entire block as if empty.
    * Use the cryptographic hash (random oracle) to compute associated seed for the round.

### Choosing Private Key well in advance of the seed.

* Computing seed `r` requries users secret key chosen before `r-1`.
* When computing the seed for `r`, Algorand uses keys for `r-k`, making it difficult for the prediction of the seed for `r`.

## Block Proposal

* Ensure block proposed in each round - threshold for block proposal role > 1.
* Found that threshold as **26** was found to be a reasonable number.
* **Minimizing Unnecessary block transmissions**
    * Problem: each node gossips it's own block - large blocks incur significant communication cost.
    * Reduce cost: sortition hash used to prioritise block proposals.
    * Algorand discards messages about blocks that do not have highest priority.
    * Gossips two messages:
        * Priorities and proofs of chosen block proposers.
        * Entire proposed block. (including solution hash and proof)
* **Waiting for Block Proposals**:
    * Each user waits a certain amount of time to receive blocks via gossip.
    * Determining wait time for blocks:
        * Scenarios:
            * When user starts waiting, they may be one of the first to reach consensus in round `r-1`, user must wait until round is complete.
            * At this point, highest priority in round `r` will propose a block.
            * Waiting will mean no received proposals.
            * If no block received after certain time - proposes empty block.
        * Algorand estimates variance in time taken to finish consensus.
            * Even if estimates were incorrect, algorithm would still remain correct.
* **Malicious Proposers**:
    * If some block proposers were malicious, worst case scenario is they trick users into initializing consensus with different blocks.
    * If adversary is not the highest priority proposer, they can propose an empty block and prevent real transactions from being confirmed.
    * BA-star has two phases: 
        1. Agreement on a block (binary value)
        2. Agreement on a bit that relates to the block chosen in phase 1.
    * BA-star guarantees:
        * All honest users agree on same block. Ensures Algorand's safety.
        * More than a fraction *T* of the weighted users are both honest and start with the same block, all honest will agree on that block.

#### Initialization:

* BA-star starts on each node.
* Block is the highest priority received block during proposal.
* BA-star tries to reach consensus.
    * If reached on non-empty block: at least *T* fraction of users initialized with same block. 

#### Starting a step

* Invoke ``sortition()`` to check user participation.
* If the user chosen, gossips signed message containing start value (either block or ACCEPT/REJECT)

#### Counting Votes

* In each step, ``MessageHandler()`` counts votes each user received for each value.
* As soon as ``T * threshold committee`` votes, Moves to completion.


#### Step Completion

* Completion distinguishes between two phases of BA-star.
* If another step required, then sets a timer for completion.
* First two steps implement reduction from arbitrary value agreement.
    * First step: committee members gossip hash of block received.
    * Second Step: Gossip hash received sufficient amount of votes, or bottom if no vote.
* In all following steps, committee members vote to accept/reject.

#### Agreement on a bit

* To agree on a bit, user counted vote sufficiently enough times.
* If neither `ACCEPT` or `REJECT` received enough votes, then `TIMEOUT`.
* Calls ACCEPT if sufficient enough, or REJECT if sufficient enough.

### Analysis

* Fraction of weighted users must translate to a sufficiently honest committee.
* Due to probabilistic nature of how committee members are chosen, small chance that malicious and honest ratio may fail to satisfy constraints.
* BA-star may fail to make progress because some users disconnected, but should not fail to make progress because of an unfortunate sortition outcome.
* Given a suitable committee choice threshold, BA-star ensures safety even if adversary can control network and create partitions.
* Guarantees that it cannot fork even in the presence of a powerful attacker.

## Algorand

* **Bootstrapping**:
    * A common genesis block provided to all users.
    * Algorand must use suer's public keys from round `r-k` to ensure seeds remain pseudo-random.
* **Bootstrapping new users**:
    * Users joining system need to find current state.
    * Algorand generates a *certificate* for every block agreed through consensus.
    * Certificate is an aggregate of messages from BA-star, sufficient for users to reach same conclusion.
    * Users need to check that all information is correct (hash and proof).
    * Certificates allow new users to validate prior blocks.
    * Potential risk: adversary providing invalid certificate.
        * Adversary must control greater than threshold in step of BA-star algorithm.
        * If committee size is large enough, this probability is negligible.
* **Forward Security**:
    * Attackers attempt to corrupt users over time (or corrupt machine and get priv. key).
    * Algorand resists attacks by having users commit to different message-signing public key for every step and publish commitments on the chain.
    * This ensures that when an identity is exposed, they already have deleted the private key.
    * To limit number of private keys users must make, **assumption** is that no network partition for long epoch.
* **Gossiping Blocks**:
    * Chosen suers can gossip new blocks before adversary learns identity and DoS.
    * Algorand's blocks are larger than maximum packet size, so inevitable that blocks sent before others.
        * Fast adversary can take advantage of this.
        * DoS any user that sends more than one packet.
    * Liveness guarantees are different in practice, instead of providing liveness in case of immediate DoS, instead provides liveness as long as adversary cannot mount targeted attack.
* **Rich Users**:
    * Ensure that users with more than threshold of money are not under-represented, these users split their money with several keys
    * Have **more than one vote**.
* **Scalability**:
    * Communication cost depend on expected size of committee and number of block proposers.
    * Algorand forms random network graph, theoretical analysis is that dissemination time grows with diameter of graph (logarithmic to number of users). 
    * Performance is only slightly affected by high number of users.

## Evaluation

### Latency

* Experiments show latency below 1 minute with up to 50k users.
* Experiment to combat bottleneck of CPU/bandwidth - latency is 3 times higher, scaling performance remains flat up to 500k users.

### Throughput

* Varying block sizes:
    * 2MB block in **40 seconds**
    * 10 MB Block size - algorand commits 450MB of transactions per hour.

### Cost of running

* CPU, network and storage costs.
* Stores block certificates to ensure block is valid.
* Each block certificate is 600KB, 60% storage overhead for 1MB block.

### Misbehaving and offline users

* Results show Algorand performs well as long as enough users present to reach consensus.
* BA-star hangs if not enough users. 
* Performance impacted by weighted fraction of malicious users.
