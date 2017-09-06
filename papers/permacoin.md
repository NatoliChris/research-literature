# Permacoin: Repurposing Bitcoin Work for Data Preservation

Andrew Miller, Ari Jiels, Elaine Shi, Bryan Parno, Jonathan Katz

```
@inproceedings{miller2014permacoin,
  title={Permacoin: Repurposing bitcoin work for data preservation},
  author={Miller, Andrew and Juels, Ari and Shi, Elaine and Parno, Bryan and Katz, Jonathan},
  booktitle={Security and Privacy (SP), 2014 IEEE Symposium on},
  pages={475--490},
  year={2014},
  organization={IEEE}
}
```

---

## Summary

### Motivation

* Make a new, quicker algorithm for Bitcoin.
* Re-utilise the mining resources so not all goes to waste.

### Problem

* Proof-of-Work is too slow and requires high resources.
* Proof-of-Work cryptopuzzle has no use.

### Contribution

* Proof-of-Retrievability - proof that requires accessing a local file.
* Permacoin chain - alternative to Bitcoin that provides reuse of mining hardware.

---

## Introduction

* Mining generates coins in Bitcoin
    * Working out a crytopuzzle.
    * Make a *Computational Investment*
* Bitcoin network requires massive amount of natural resources.

### Goal and Approach

* Bitcoin resources can be repurposed.
* Make mining depend upon storage resources rather than computation.
* Proof-of-Retrievability:
    * Proves a node is investing memory or storage resource to store a fragment.
    * Essentially highly distributed storage.
    * Distribute file F to protect against data loss.
* Does not require reputation

### Challenges

* Constructing adversarial model for new setting and presenting distributed PoR that is secure. 
* Ensure clients maintain independent storage devices.
    * Pooled cloud would reduce robustness.
* Enforce locality of storage
    * Block access depends on *private key*.
    * Access made sequentially and pseudorandomly.

### Contribution

* Resource recycling of bitcoin resources.
* PoR distributed penalizing storage outsourcing, rewarding local storage.
* A new model for the Bitcoin blockchain.

## Preliminaries and Background

* Each epoch in Bitcoin a new puzzle known to all participants.
* All miners race to generate a winning ticket for the puzzle.
    * Miner that wins receives coins as reward.
* Bitcoin:
    * Solving puzzle validates history of transactions.
    * Successful miner obtains payment by including in transaction list a payment of new coins to themselves.
* Design Challenge:
    * Predictable effort: Bitcoin adjusts difficulty every 2016 blocks.
    * Fast verification: Puzzle solution is easy to verify.
    * Pre-computation resistance: infeasible for a client to pre-compute winning ticket
    * Linearity of expected reward: Expected reward approximately constant.
    * Repurposing: resources committed to mining can be repurposed.
* Proofs of Retrievability:
    * A basic POR is challenge response.
    * *Prover* `p` demonstrates possession of file `F`.
    * Three methods:
        * ``Setup(F)``: Erasure code to store segments.
        * ``Prove(puz, R, F^)`` : Proof that contains merkle tree
        * ``Verify(digest,...)``: validates merkle tree given by proof.
    * PoR provides strong guarantees.
    * The adoption for the blockchain differs from other proposed PoR:
        * Verifiers is the same as the blockchain.
        * Every client acts as a power - number of provers is large.
    * Proofs generated asynchronously with large groups.
        * Only previously they were synchronously.
    * PoR implicitly incentivises the correct storage.

## Security Assumptions

* Bitcoin:
    * Each client has key pair - used to sign transactions.
    * No pre-established identities and new identities created at any time.
* Permacoin:
    * Extremely large archival data file too large to store from individual.
    * Benefit of storing across peers is that it is more durable.
* Globally, therefore, security goal is to preserve *F* even with failures.

### File Distribution

* Assume *F* comes from single authoritative dealer.
* Assume newly created clients download fragments of *F*.
* Storage of *F* distributed provides robustness from failures.

### Limited Adversary

* Adversary that controls a small fraction of clients.
    * Fundamental requirement of Bitcoin.
* Remaining clients are honest and independent.
    * Seek to maximise gain in mining and investment.

### Local Private-Key Storage

* Substantial fraction of clients do not share private keys externally.
    * Sharing keys mean sharing coins and exposing to theft.
* Substantial fraction of clients store private-key locally.
* Construct puzzle to have **continuous use of miners private key**.

## Scheme

* Effective way to solve puzzle is to store portions of the dataset.

### Simple PoR Lottery

* Two Issues associated with effort of PoR:
    * Choosing random subset of segments based on participants pubkey.
        * Each participant may not have sufficient storage.
        * Participant chooses random subset segments based on pubkey hash.
    * Non-interactive challenge generation.
        * Traditional PoR - verifier sends challenge.
        * Since chain is verifier, challenge generated non-interactively.

### Local PoR Lottery

* PoR does not incentivise distributed storage.
*
