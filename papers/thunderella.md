# Thunderella: Blockchains with Optimistic Instant Confirmation

Rafael Pass, Elaine Shi

```
@misc{pass2017thunderella,
  title={Thunderella: blockchains with optimistic instant confirmation},
  author={Pass, Rafael and Shi, Elaine},
  year={2017},
  publisher={Manuscript}
}
```

---

## Summary

### Motivation:

- Blockchains need instant transaction confirmations.
- Current efforts in Blockchain OR BFT cannot/do not cope properly.

### Problem:

- BFT research does not try to reach "responsiveness".
    - Responsive iff any transaction input to honest node confirmed in time that depends only on the actual network delay - not a-priori known upper bound.
- Blockchains do not operate quickly, no instant transactions.
- Recent efforts fall short of this and don't consider node presence.

### Contribution:

- Thunderella
    - Asynchronous BFT with fallback to PoW.
    - Handles node participation with *sleepy model*.
    - Instant in optimistic (![3/4](https://latex.codecogs.com/gif.latex?%5Cfrac%7B3%7D%7B4%7D) honest) case.

---

## Abstract

- State Machine Replication is central abstraction for distributed system.
- New paradigm, **Thunderella**, for achieving state machine replication combined with fast path with a slow fallback.
- Optimistically instant transaction confirmation if super majority of players honest.

## Introduction

- State machine replication, atomic broadcast, core distributed system abstraction.
    - Linear Ordered Log such that:
        - **Consistency** - all have same view of log.
        - **Liveness** - whenever a client submits a transaction, incorporated quickly into log.
- State machine replication is fundamental building block for distributed databases.
    - Classical deployment scenarios:
        - Small scale.
        - Crash tolerant rather than Byzantine tolerant.
- Large scale applications of distributed consensus in two deployment scenarios:
    - *Permissionless*: anyone can join freely.
    - *Permissioned*: Only approved parties may join.
- Two broad classes of protocols considered:
    - **Classic Style Protocols**:
        - Fast confirmation.
        - Low Scalability.
        - Dififcult maintenance.
        - Tolerate 1/3 corruptions.
        - E.g. PBFT, Paxos.
    - **Blockchain Style Protocols**:
        - Robust.
        - Large scale.
        - Tolerate minority corruptions.
        - Slow transaction confirmation.
        - Bitcoin, etc.

### The Thunderella Paradigm

- Responsiveness
    - Any transaction input to an honest node is confirmed in the time that depends only on the actual network delay, but not on any a-priori known upper bound on the network delay.
    - ![Big-Delta](https://latex.codecogs.com/gif.latex?%5CDelta) for a-priori known delay.
    - ![Small-Delta](https://latex.codecogs.com/gif.latex?%5Cdelta) for network delay.
- Achieving responsiveness requires knowledge that majority of players honest.
- **Optimistic responsiveness**: responsiveness only required to hold when "goodness conditions" satisfied.
    - Worst-case conditions (`W`) - under which the protocol provides worst-case guarantees. Slow.
    - Optimistic-Case conditions (![`O Subset W`](https://latex.codecogs.com/gif.latex?O%20%5Csubseteq%20W)) - protocol provides responsive communication (O > 3/4 honest and online)
- **The Idea in a Nutshell**
    - Designated Entity, *leader* or *accelerator*.
    - Transactions setnt to leader; leader signs transaction with sequence number increasing, sends to committee of players.
    - Committee member `ACK` all leader-signed transactions, at most one per sequence number.
    - Transaction has received more than 3/4 committee signatures - referred to as **notarized**.
        - Participants, can directly output their longest sequence of consecutive notarized transactions.
        - The output transactions are confirmed.
- Protocol consistent under ``W' = 1/2 committee honest`` and satisfies liveness with optimistic ``O = 3/4 honest`` responsiveness.
    - 2 communication rounds to confirm transactions.
- **Problem**
    - No liveness under `W'`.
    - If the leader is cheating, the protocol halts.
    - To overcome the problem, the underlying blockchain protocol is leveraged.
- Fallback to PoW as a *'cooldown period'*.
    - No signing of transactions.
    - Length of period is counted in blocks on the blockchain.
    - After cooldown, safely enter a *slow period* where transactions only get confirmed in the underlying blockchain.
    - Use blockchain for new leader.
- Reason for **cool-down** period:
    - Players may disagree on the transactions being confirmed before entering the slow period.
    - Cool-down allows for honest players to post notarized transactions they have seen to preserve consistency.
- **Collecting Evidence of Cheating**
    - If a player notices his transaction is not getting confirmed by the leader, he can send the transaction to the underlying blockchain.
    - Leader required to sign tx on the chain - if the tx still not signed then broken leader.
    - Cannot falsely accuse leader.
- **Selectiveness of Committee**
    - Two different approaches:
        - **All players as committees**:
            - Simplest approach.
            - Trivially have resilience under `W`.
            - Subsample amongst a set of players. (Random as in AlgoRand)
            - May also be used in permissionless system as Thunderella used to construct crypto-currency.
        - **"Recent miners" as Committee**
            - Select recent miners of blocks.
            - Relies on underlying blockchain to be **"fair"** - where fraction of honestly mined block is close to the fraction of honest players.
                - Not the case in Nakamoto's original Blockchain. (Fruitchains paper [PODC2017] - Shi and Pass)
- **Permissionless Thunderella**
    - Applying second approach, underlying PoW based blockchain:
        - Assume PoW as random oracle.
        - Exists a state machine replication protocol that achieves consistency (non-responsive) and liveness in permissionless environment.
        - Moreover, if more than 3/4 of the online computation power is honest and online, protocol achieves responsiveness (after warm-up).
- **Permissioned Thunderella**
    - Similar theorems shown for permissioned environment.
        - Classical mode is standard synchronous model.
        - All nodes spawned upfront and identities and keys provided to protocol as input.
        - Crashed nodes are treated as faulty.
        - Theorem:
            - Assume existence of PKI and OWF (One way function).
            - Exists state machine replication protocol that achieves consistency and liveness in classical environment under any ``f < n``.
- **Sleepy model** (Pass and Shi) captures **Sporadic Participation**.
    - Permissioned in nature that the set of protocol participants and their public keys are a-priori known.
    - Shows that achieving consensus when majority online are honest.
    - Theorem:
        - Assume existence of PKI and enhanced trapdoor permutations and common reference string.
        - Exists state machine replication protocol that achieves consistency and liveness in sleepy environment with static corruptions.
            - 1/2 - ![`epsilon`](https://latex.codecogs.com/gif.latex?%5Cepsilon) online are honest for any arbitrary small epsilon.
        - If more than 3/4 nodes honest, protocol achieves responsiveness.
- **Lower Bound on Optimistic Honest Threshold**
    - Additionally prove that optimistic bound of 3/4 is tight.
    - No protocol can be optimistically responsive when more than 1/4 are corrupted.
- **Practical Considerations: Instant confirmations and scalability**:
    - Build on top of current protocol.
    - Only send transactions to the leader.
- **Practical Considerations: Instant confirmations and scalability**:
    - Build on top of current protocol.
    - Only send transactions to the leader.
    - Leader has possible payment to incentivise to do job correctly.
- **Comparison**
    - Similar to classical-style protocols such as PBFT and Paxos.
    - Normal path gets stuck - BFT-Style protocol falls back to a view change mechanism.
    - Worst case this protocol falls back to synchronous underlying PoW.
    - Thunderella is also a constant factor faster than most PBFT- or Paxos- protocols.
        - BFT-Based require multiple rounds of voting even in normal path, Thunderella just 1.
        - It is possible to compress rounds, but require more rounds for resilience.

### Related Work

- State machine replication: classical and blockchain-style approaches.
    - State machine replication extensively investigated and widely adopted.
    - Blockcahin uses different properties to classical.
    - Treats all unresponsive nodes as faulty.
    - Blockchain consensus handles dynamic participation, BFT requires static participation.
    - Proof-of-Stake can be used to manage identities.
- Other closely related works.
    - "Hybrid Consensus" achieves responsiveness.
    - Earlier works look at less rounds, synchrony and don't have a goal of responsiveness.
    - No fallback in other models, they simply remove the liveness.
- Relevance to Ethereum's current efforts:
    - Ethereum has a 2 stage agenda;
        1. Proof-of-Stake and Proof-of-Work co-exist, while the difficulty rises.
        2. Stake holders will vote on transactions or blocks atop of existing PoW. Penalty based voting. Finally move to pure PoS.
    - However, not Ethereum's goal of instant transaction confirmation.
