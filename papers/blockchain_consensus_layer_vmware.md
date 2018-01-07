# The Blockchain Consensus Layer and BFT

Ittai Abraham, Dahlia Malkhi (VMware Research)

```
@article{abraham2017blockchain,
  title={The Blockchain Consensus Layer and BFT},
  author={Abraham, Ittai and Malkhi, Dahlia and others},
  journal={Bulletin of EATCS},
  volume={3},
  number={123},
  year={2017}
}
```

---

## Summary

### Motivation:

- Blockchains are distributed systems that use consensus to operate.
- Previous BFT-based protocols were used to reach agreement, blockchains are different.
- There are connections between blockchains and BFT, with possible opportunities to have hybrids.

### Problem:

- Blockchain consensus is still relatively new to most.
- There is no literature summarising the blockchain consensus space.

### Contribution:

- A comparison between blockchain protocols and BFT protocols.
- Discussion of hybrid blockchain and BFT opportunities.
- Analogies and formalisation of some blockchain consensus.

---

## Introduction

- Bitcoin is a method of reaching agreement on a shared chain of blocks containing a sequence of transactions.
- Hashcash was one of the first to use PoW for emails.
- Aspnes et al. presented PoW as a solution for Sybil attacks.
- B-money was a crypto-currency system that used crypto-puzzles for minting currency.
- Challenge of preventing double spending in a permissionless setting remained unsolved until Bitcoin.
- Blocks buried deep in the chain are considered committed with high probability.

### Layered View of the Blockchains

- **Layer 1 - State Machine Replication**
    - Bottom layer infrastructure for storing immutable chain amongst distrustful parties.
    - Layer builds a chain of consensus blocks.
    - Fundamental instrument is the State-Machine replication.
- **Layer 2 - Smart Contracts**
    - Simple shared datastructure with high level abstractions.
    - Functionality provided through smart contracts.
    - Programmatically verify/facilitate/enforce negotiation of business transactions with logic.
- **Layer 3 - Services**
    - Top layer is applications where customer value created.
    - Integrated applications and services that interact with the blockchain.

## Nakamoto Consensus through the Lens of the theory of distributed computing.

- Byzantine agreement solves consensus.
- Blockchain solves double spending.

### Adversary Model

- Traditional Adversary Model: assumes thresholds in participant numbers.
    - Number of nodes (`n`)
    - Number of adversary (`f`)
    - Usually ``n > 2f`` or ``n > 3f``
- Computational Threshold Advesary for bitcoin PoW:
    - Power total (`N_c`)
    - Advesary Powern(`F_c`)
    - Usually ![``N_c > 2F_c``](https://latex.codecogs.com/gif.latex?N_c%20%3E%202F_c) or ![``N_c > 2(1 + epsilon)F_c``](https://latex.codecogs.com/gif.latex?N_c%20%3E%202%281&plus;%5Cepsilon%29F_c)
- Stake Threshold Adversary for Proof-of-Stake
    - Total amount of Resource R (`N_r`)
    - Total Adversary Controlled Resource (`F_r`)
    - Usually ![``N_r > 3F_r``](https://latex.codecogs.com/gif.latex?N_r%20%3E%203F_r)

### Game Theoretic Model

- Instead of designing against a malicious adversary, design against coalitions.
- Protocol to be coalition resilient in equilibrium.
- Differences
    - Malicious model states all parties act independent or arbitrary.
    - Game Theory designs against coalitions forming to **maximise gain**.
### Computational Threshold Model

- **Synchrony**:
    - Network delay unbounded then the adversary can boost computational power by delaying parties.
    - Computational Threshold Adversary model incorporates synchrony, whereas traditional considers both.
- Bitcoin uses PoW to implement a leader election *oracle*. **(PCNELE Method)**:
    - **Independence**: Each party elected independently.
    - **Fairness**: Probability is proportional to the party's computational power.
    - **Pre-Commit Non-Equivocation Leadership Announcement**: Each round each party commits to an action, oracle elected and announces action.
- Fairness crucial for distributed protocol.
- Pre-commit is important so that they do not announce different blocks.
- Implementing PCNELE with Crypto-Puzzles:
    - **Pre-commit**: Solving the puzzle requires committing to specific block.
    - **Public Verifiability**: Solver can generate a solution certificate of his solution.
    - **Fairness**: Probability of solving the puzzle is proportional to the computational power.
- This assumes a *partially synchronous* model. (Rounds)

### Nakamoto Consensus Protocol - Longest Fork Wins (LFW)

- Nakamoto Consensus implements a replication of a blockchain using PoW and LFW.
- LFW:
    - When pre-committing block content, party must pre-commit to a reference of the previous block.
    - LFW rule is to **always** choose the longest chain.
- Abstract Nakamoto Consensus protocol: Implementing Blockchain Replication
    - **New Block via FPCNELE Oracle**: Oracle allows each leader to announce single pre-committed new block.
    - **Longest Fork Wins**: Non-faulty leaders will connect their pre-committed block to the leaf block, forming longest path to genesis block.
- **Blockchain Replication**
    - Goal of SMR is to form growing log of commands.
    - Blockchain Replication Protocol has to deal with *forks*.
        - **Forks**: instances of uncertainty about which block to decide on.
    - **Properties**:
        - *Uniqueness*: Unique deterministic function to extract single path from G.
        - *Liveness*: the path L(G) is constantly growing.
        - *Safety*: L(G) is a path of length `k` for `G` and `G` grows to become `G'` and the probability that any block that was in the longest path is no longer in the longest path exponentially decreases.
            - (In short: probability of a block being committed increases with the number of blocks)
        - *Fairness*: The proportion of nodes in P that belong to non-faulty parties is at least proportional to their relative computational power.
- **Analyzing abstract Nakamoto Consensus**
    - IF non-faulty parties follow LFW principle, shown that properties are upheld.
    - The block is buried so deep it will not be replaced - adversary manages a long fork.

### Nakamoto Consensus - A Concrete Protocol

- Concrete Protocol:
    - Given a party `i` that wants to commit a block with transactions `o`.
    - Let ![``P = a_1,...,a_k``](https://latex.codecogs.com/gif.latex?P%20%3D%20a_1%2C%20...%20%2C%20a_k) be the longest path that party `i` attempts to solve.
    - It will announce ![``a_k <-- b``](https://latex.codecogs.com/gif.latex?a_k%20%5Cleftarrow%20b) where `b` is a new block.
