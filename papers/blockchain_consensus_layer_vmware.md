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

## Relating NC to BFT

- When an NC leader announces a block, implicitly announces any missing blocks from G.
- Longest Fork Wins protocol the leader proposal wins if the path is the longest in G.
- Relating to literature - there is a common theme where proposals prioritised.
    - **Non-Equivocation**: leaders prevented from equivocating, so that there is only one possible proposal.
    - **Proposal-Safety**: a high-ranking proposal may extend, but not modify, any lower ranking log prefixes.
- The NC and BFT differ in how they accomplish principles, BFT has **finality**.
- **SMR Problem Model**
    - SMR well known for building a fault-tolerant service for linearising updates.
    - Traditional adversary model - a threshold of the replica set may suffer failures.
    - Focusing on consensus core, key challenge is to reach agreement:
        - **Agreement**: There is agreement on a sequence of decisions amongst all correct replicas.
        - **Validity**: Committed decisions form a monotonically growing list of commands. Each command proposed by a replica.
        - **Liveness**: If a correct replica proposes a command, then eventually some decision is committed.

### Asynchronous Benign Framework (N = 2F + 1)

- Benign failure model - focusing on safety.
- Dwork, Lynch and Stockmeyer (DLS) widely employed for SMR (Viewstamped Replication, Paxos, Raft etc.)
- Key concept is an explicit ranking amongst proposals.
    - DLS ranks are termed *phases*, in Paxos *ballots*, Viewstamped *views*.
    - Replicas start with initial view. Progress from one view to the next.
    - Commands accepted in the highest view to have started.
    - Liveness relies on having constant fraction of views with a good and timely leader.
- Pipelining:
    - Practical SMR solutions let the leader of the current view drive repeated extensions to the log.
    - Optimized pipelining of proposals.
    - Stable leader avoids waiting for current view to expire for each command.
- Correctness: Protocol guarantees Agreement because a leader proposes only *safe* proposals. Value is safe if no lower view decides a conflicting value.

### Asynchronous Byzantine Framework (N = 5F + 1)

- For the byzantine model, the adversary can perform arbitrary actions on the faulty parties.
- Messages are signed to prevent spoofing.
- Quorums of the SAFE mechanism:
    - Used to collect information about lower views picking safe values to propose.
    - Need to be adjusted to guarantee intersection in sufficiently many non-faulty replicas.
- ACCEPT component requires a mechanism that prevents a leader from equivocating and sending conflicting proposals to different parties.

### Synchronous Byzantine Framework (N = 3F + 1)

- Adapting from asynchronous to synchronous means a party can collect messages from all non-faulty replicas by waiting the number of maximal transmission durations.
- Algorithm:
    - Leader sends a signed `PROPOSE`.
    - Replica accepts first `PROPOSE` it receives and echoes it with own signature.
    - A replica runs `DECIDE` if it hears from ``N-F`` `PROPOSE` messages.
    - Safe if highest view with ``F+1`` replicas accepting identical value, else `bottom`.
- Reaches Agreement because decision reached in round  where all correct replicas accept some proposal and no correct replica accepts a different proposal.

### Byzantine Frameworks with Optimal Resilience.

- Two previous protocols do **not** have optimal resilience. The trick is to replace the leader's broadcast with a protocol that provides two guarantees.
    1. Leader cannot equivocate.
    2. Every non-faulty replica that echoes a leader proposal can prove that it is unique.
- Replace trivial broadcast with two-phase broadcast.
- **Asynchronous with N=3F+1**
    - Leader proposal works in two phases
        - Leader sends a signed `PRE-PROPOSE` proposal. Replicas echo adding own signature that they receive from leader or other replicas.
        - Upon receiving same value from 2F+1 `PRE-PROPOSE` or F+1 `PROPOSE` messages, replicas echo a signed ACCEPT message and accept the proposal.
    - Replicas accept the decision if it has received ``N-F`` `PROPOSE` messages.
    - For it to be SAFE, leader chooses the value whose view is the highest such that:
        - ``F+1`` replicas sent a propose with the value.
        - ``2F+1`` replicas sent a `PRE-PROPOSE` for the value.
        - ``F+1`` replicas sent a `PRE-PROPOSE` for it.
        - Else selects bottom.
- **Synchronous Byzantine Framework with N=2F+1**
    - Implement leaders broadcast using two all-to-all exchanges.
        - First phase - leader broadcasts the `PRE-PROPOSE` value, which is echoed by replicas.
        - Second Phase - A replica accepts a value if all `PRE-PROPOSE` echoes (F+1) match.
    - A decision can be reached with F+1 `PROPOSE` carrying identical values.
    - No conflicting `PROPOSE` and `PRE-PROPOSE` messages.

### Back to Nakamoto Consensus

- Nakamoto Consensus seems very different to SMR.
- NC utilises synchrony assumptions and cryptographic puzzles to progressively make decisions harder to revoke.
- Like BFT, each party has view 0 (Block 0) and moves from one view of the chain to the next.
- The leader of the view `v` solves a puzzle for chain length ``v-1``, it then proposes to all.
- The NC Propose is self validating, causes all parties to move to `v` when they receive the value.
- Key observationis that a party acceptance is implicit by the collaborative work represented in the puzzle solution.
- An agreement on blocks buried `k` level deep in ``L(G)`` holds with high probability.

## Combining NC with BFT

### Blockchain Replication Challenges

- **Lack of Finality**:
    - Blockchain replication suffers from a long-term deficiency where always a risk that a block with be uncommitted.
    - Formal safety seems to require infinite executions to assume that the computational threshold minority will stay true for the entire execution.
    - Raises many concerns. (What if an adversary waits for a long time and eventually gets majority power)
- **Commit Latency**:
    - Suffers from short-term deficiency.
    - Assume infinite execution, willing to assume that a block is buried by an hours worth of cryptographic puzzles.
    - BFT provides instant finality - property that NC does not achieve.
- **Selfish Mining**:
    - Attack on the fairness property. Related to the incentives behind the PoW.

### BFT State Replication Challenges

- SMR brings many advantages.
    - Does not suffer from lack of finality.
    - Once block is committed, never revoked.
    - SMR have very low latency and high throughput.
- The permissionless model of NC allows for much larger sets of replicas.
- BFT SMR weem to work well for few tens of servers, scaling to hundreds and thousands is a challenge.

### Bridging the Gap

- Combining the NC and BFT approaches to overcome challenges.
- Ethereum Casper harnesses BFT to deal with lack of finality in NC.
    - Sets a trusted committee of validators selected by placing security deposits.
    - The committee post-validates the NC blocks via BFT.
    - After a path is agreed by the validators, it is considered committed.
- Another approach is using a rotating committee, the committe is responsible for deciding on the blocks to add to the chain.
- Bitcoin-NG (ByzCoin) suggest that a leader is elected to perform micro-transactions. ByzCoin said that the committee was the last C elements in the chain.
- Third approach in Solidus, it does not use LFW  but uses PoW to mint new currency and select a committee.
    - Newly minted block does not win LFW, needs to go through consensus approval by existing committee.
    - Once a block is added to the chain, rotates the committee.
    - Solidus provides finality AND low latency.
- Scaling Byzantine agreement through a randomized sample sub-comittee is, in itself, a known idea.
- A vulnerability of all committee-based approach is that the adversary can target the commitee members directly.
- Algorand uses Proof-of-Stake coupled with a verifiable random function to select members.
    - It further mitigates vulnerabilities by having each comittee determine only one block, converging in one communication step by each member.

## Concluding Remarks

- Aspects of Blockchain as Byzantine Fault Tolerant protocol.
- SMR engine of the blockchain only provides the base of the stack. Future work may cover smart contracts where there are fascinating connections between service logic and the area of completely removing trust from centralized implementations.
