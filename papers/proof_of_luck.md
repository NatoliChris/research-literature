# Proof of Luck: an Efficient Blockchain Consensus Protocol

Mitar Milutinovic, Warren He, Howard Wu, Maxinder Kanwal

```
@inproceedings{milutinovic2016proof,
  title={Proof of Luck: An efficient Blockchain consensus protocol},
  author={Milutinovic, Mitar and He, Warren and Wu, Howard and Kanwal, Maxinder},
  booktitle={Proceedings of the 1st Workshop on System Software for Trusted Execution},
  pages={2},
  year={2016},
  organization={ACM}
}
```

---

## Summary

### Motivation

* Provide a secure algorithm for selecting participants to participate in consensus.
* Bitcoin needs improvement.

### Problem

* Proof-of-work is slow, requires reduced forks. 
* Require 6 blocks for confirmation.
* Bitcoin is *"impractical"*.

### Contribution

* Proof-of-luck algorithm using Intel SGX achieving low latency.
* Consensus primitives that use a TEE (trusted execution environment).

---

## Introduction

* Proof-of-Work is robust against byzantine.
* To reduce number of forks, PoW designed for 10 minutes per block.
* Trusted execution environment can be used for decentralizes electronic currency.
* TEEs limit the effect of Sybils.

## Related Work

* Bitcoin requires high energy consumption.
* It remains unclear whether proposed solutions maintain security properties and incentives.
* Alternate approach include replacing the puzzle with something meaningful, but still requires the computational resources.
* Practical Byzantine Fault Tolerant algorithms
    * Do not scale.
    * Relies critically on network timing.
    * Maintained list of participants.
* To improve confirmation times, many approaches:
    * Uncle blocks.
    * Divided into keyblock/micro blocks.

## Problem Definition

* Problem is that distributed nodes must agree on a single shared state.
* Current designs are slow using significant time in consensus mechanism.
* Validation prevents arbitrary state change, but still allows "forks" to occur.
* Participants incentivised to prefer own state rather than accept others due to block reward.
* Goal of paper:
    * Quick, deterministic transaction confirmation.
    * Energy and Network communication efficient protocol.
    * Resistant to custom hardware not commonly available.
    * An attacker controlling under a threshold of TEEs can not control the blockchain.
    * An attacker cannot control the blockchain without controlling the majority of CPUs without breaking the TEE platform.
    * No requirements for synchronized clock between participants.
* **Assumptions**:
    * Participants have CPUs that implement suitable TEEs (e.g. IntelSGX).
    * TEE programs can produce unbiased uniform random numbers that can't be influenced by the attacker.
    * TEE programs can detect concurrent invocations.

### Principles

* Participants are the main principals in our protocol.
    * Use a TEE and perform routines to maintain the chain.
* Trusted Platform Vendor controls correct execution of TEE.
* Clients rely on the blockchain and communicate with participants.

### Threat Model

* Adversary controls some fraction of the participant's machines.
* Assume adversary cannot break cryptographic primitives with non-negligible probability.
* Adversary can run the protocol in a TEE for each controlled machine, cannot generate a valid attestation proof if they deviate.
* Adversary can read and send it's own network messages, cannot modify messages.
* Messages in protocol do not identify sender, although some implementations may choose to do so (TCP/IP).
* Assumed adversary can spoof sender information in it's own message and tamper with senders information from honest participants.

### SGX Background

* Isolation and remote attestation features, SGX has a platform of trusted services that provide relative timestamps and monotonic counters.
* In the remote attestation protocol, TEE certifies result with signed report and verifiable quote.

## Building Blocks

### Proof-of-Work

* Discussion whether PoW should be ASIC resistant.
    * TEE enabled PoW sidesteps issue as only supported platforms can mine.
* Algorithm:
    * Wraps ``<nonce, difficulty>`` to the TEE attestation.

### Proof-of-Time

* Proof-of-work enforces that a sufficient amount of time has passed.
* TEE can enforce directly by waiting for the designated time, saving CPU cycles/energy.
* Utilise a ``SLEEP`` function to sleep for the duration.
* A malicious participant may try to game proof-of-time by running instances in parallel.
    * TEE has the monotonic counter to prevent this.

### Proof-of-ownership

* Countermeasure against Sybil attacks.
* When participants are required to use a TEE, limits participant from owning more than threshold of CPUs.
    * EPID in Intel SGX in *name base* mode.
* Proof-of-Nonce (tee attestation: ``<Nonce>, nonce``)
* Consensus block chosen is the one with most unique pseudonyms.

## Proof-of-Luck

* Two Functions:
    * **PoLRound**
        * Start of every round, preparation to mine.
        * Passes the latest block.
    * **PoLMine**
        * Called to mine a new block.
        * Pass header of the new block to the block it will extend.
        * Generates a random value from a uniform distribution used to determine winning block.
* To optimize protocol communication, algorithm delays releasing the proof depending on number of people selected.

### Protocol

* Every participant in the protocol receives transactions from clients and other participants.
* Every round, participants execute algorithm to commit pending transactions into a new block.
    * Block contains Proof-of-Luck
* Participants broadcast new chain to others if the chain be luckier than any alternative chain received.
* When a network split heals, the larger half of the chain is likely to have greater luck.
* Before broadcasting the participant may start a new round of mining.
    * Happens if the new chain has a different *parent* block from it's latest block.
    * Depending on the network split, if a luckier chain is received when split resolved, mining may need to restart.

## Analysis

### Persistence against Minority Attacker

* Several blocks after a fork, it is exponentially improbable for a minority attacker to produce a chain preferred by the majority of honest participants.
* Utilises a Chernoff bound to show that the probability of minority winning is exponentially small.

### Proportional Control of Blocks

* At each round, every participant has equal probability of generating the largest random number, because the participants sample independently from identical distributions.
* Among honest participants that append to the longest chain, the new chain with largest luck value in newly added block is preferred.

### Round Time and Confirmation Time

* 15 seconds of round time.
    * Evaluation of information in the Bitcoin network.
        * Median block propagation time has been observed to be around 6.5 seconds.
* Initial selection of winning block with maximal luck value can be implemented without transmitting whole blocks (only headers).
    * 1 round trip.
    * Block propagated only after winner determined.

## Compromised Trusted Execution Environment

* Consensus protocol assumes all requirements for TEE met.
    * Expensive to violate.
    * Motivated attacker may compromise a limited number of TEEs.

### Luckiest m

* Strengthen design against high cost attack
    * Extension to protocol
    * Blockchain consisting of "super blocks"
    * Merges `m` normal blocks and their proofs of luck.
    * Participant mines normal blocks, but selects `m` luckiest blocks to merge.
    * Future work looking at persistence property of extension.
* Similar to proof of ownership, but selecting the `m` luckiest making it more scalable.
* `m` proofs must come from different CPUs.
    * If a CPU is compromised, it is limited.

### Merging

* Honest parties broadcast block to other participants that choose the chain to extend.
* Participants merge the `m` luckiest blocks into a super-block. 
* Blocks from honest participants have *almost* the same transactions, super block compressed efficiently.
* Similar approach to Ethereum's uncle blocks.

## Conclusion

* TEE enabled building block design for blockchains.
* Combined primitives from proofs to make a new proof-of-luck system.
* Blockchain ensures liveness, persistence while providing more efficient mining and low latency transaction validation.

