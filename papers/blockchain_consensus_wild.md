# Blockchain consensus in the wild

```
@article{cachin2017blockchains,
  title={Blockchains Consensus Protocols in the Wild},
  author={Cachin, Christian and Vukoli{\'c}, Marko},
  journal={arXiv preprint arXiv:1707.01873},
  year={2017}
}
```

---

## Summary

### Motivation

* Blockchain is becoming increasingly popular, as both an aspect of society as well as in research.
* New algorithms are being implemented and used in different ways to traditional use.
* Consensus is difficult.

### Problem

* The consensus algorithms are not peer reviewed.
* Consensus is difficult, the algorithms are complex and they should be checked when implemented.
* All consensus algorithms should have formal proofs for showing that it has a superior gain.

### Contribution

* Paper offers a review of the consensus protocols offered by some of the blockchain systems.
* Provides overview of consensus protocols and properties.
* Summarised some of the permissioned blockchains and how they are defined.

---

## Introduction

- The blockchain is a trustworthy service to nodes that don't trust each other.
- Permissionless blockchains allow for public registration, anyone can be a user and contribute.
- Permissioned blockchains are operated by known entities or members of a consortium.
- Consortium blockchains are employing *Byzantine Fault Tolerant* algorithms.
	- Taking a more traditional approach. 
- Widespread interest in blockchains sparked new research into distributed consensus.
- Developing consensus protocols is difficult!
	- Protocol is only useful when continues to deliver even with Byzantine adversaries.
	- Detailed analysis, formal argumentation are **necessary** to gain confidence.
	- Any claim should be supported by formal justification.

## Trust in a blockchain protocol

- Consnesus for blockchain is similar to building cryptography.
	- Build off the experience of others.
	- Look at how it is used in practice.
- It is generally difficult to evaluate any security mechanisms.
- All security solutions should come with a clearly stated security model and trust assumption.
- Cryptosystems require peer review to note if it is secure!
- *Chubby* and *Zookeeper* used Paxos, which was formally verified and proof, but the implementations were different / difficult.
- The Blockchain has *"hype"* but requires reviews and scientific methodology in crypto, security and consensus.

## Consensus

### Blockchains and Consensus

- **Blockchain**: append only distributed database updated in batches (blocks).
- Additional integrity measures are used as compensation for the malicious users.
- The whole blockchain acts as a trusted system - it should be dependable, resilient and secure.
- The blockchain protocol ensures these by **replicating** the data for resilience **NOT** scalability.
- **State-Machine Replication** = logic to replicate. **Consensus** = same sequence of events.
- Schneider: the task of reaching and maintaining consensus can be described with two elements:
	- Deterministic state machine that imlpements logic to be replicated.
	- Consensus protocol to disseminate requests among nodes to ensure same sequence of requests.
- Asynchrony and eventually synchronous Models
	- Asynchronous network that eventually behaves synchronously.
	- Model is widely accepted as it is realistic.
	- Assumptions can quickly turn into a vulnerability.
	- Eventual synchrony is preferred, because it covers natural behaviours of the networks.
- Consensus in Blockchain:
	- Garay et al: consensus protocols can be swapped as long as similar trust model.
	- Now: Any consensus model can be used and it will still retain most of it's aspects.
	- Several protocols have static groups which require explicit reconfiguration with new nodes.

### Crash-tolerant Consensus

- Form of consensus relevant for the blockchain is the **reliable blockchain**
- Properties:
	- **Validity**: If a correct node (p) broadcasts m, then p eventually delivers m.
	- **Agreement**: If a message is delivered by some correct node, then it is eventually delivered by every node.
	- **Integrity**: No correct node delivers the same message more than once; moreover, if a correct node delivers a message and the sender of the message is correct, then the message was previously broadcast by the correct node.
	- **Total Order**: For messages M1 and M2, suppose p and q are two correct nodes that deliver m1 and m2. Then p delivers m1 before m2 if and only if q delivers m1 before m2.

### Byzantine Consensus

- Most prominent protocol is PBFT - extension of the PAXOS algorithm.
	- Tolerates ``f < n/3`` failures which is optimal.
	- Actual implementations = BFT-SMaRt.
- Like paxos the protocol expects an eventually synchronous network to make progress.
- Without the assumption, only randomized protocols for Byzantine consensus are possible.

### Validation

- Atomic broadcast resilient to crashes - every message usually considered acceptible.
- Validation is external to the consensus.
- For the permissioned protocols, the consnesus is on the raw sequence.

### Tangaroa (BFT-RAFT) neither live nor safe

- Tongaroa, improved RAFT - class project that got famous for blockchain.
- It was never peer-reviewed, never published. Eventual Synchrony model.
- Raft Consensus:
	1. Leader for each view.
	2. Timeout = new vote for leader.
- Liveness Issue:
	- Byznantine leader that doesn't do any work will stop the liveness.
- Safety Issue:
	- If the leader fails / is malicious, may cause messages out of order which may confuse the next leader.
- Byzantine Consistent: deliver the *same* payload.
- If the leader doesn't find out about the message - it will act as normal but the message will be lost.
	- Fix?
		- Reliable Broadcast.

## Permissioned Blockchains

### Hyperledger Fabric

- Platform for distributed ledger solutions.
- Separates contract execution from ordering / conflicts.
- Separation of the trust for validation and ordering.
- KAFKA:
	- Pub/Sub system
	- Broker consistency
	- Supports an arbitrary number of peer nodes.

### Tendermint

- Tendermint Core uses BFT protocol like a variant of PBFT.
- Most significant departure from PBFT is the continuous rotation of the leader.
- Tendermint embeds aspects of the view change into it's common-case pattern.
- "Byzantine Fault Tolerance in the age of Blockchains":
	- Tendermint suffers from livelock bug
- However, several mechanisms that are undocumented.
- Not peer-reviewed, but appears to be sound.

### Symbiont - BFT-SMaRt

- Focuses on financial distributed applications.
- Utilises the BFT-SMaRt protocol, looks like a reimplementation.
- Said to achieve 80k tx/s using a 4 node lan cluster.

### R3 Corda - RAFT and BFT-SMaRt

- Each state is consumed, tx consumes multiple states to produce a new state.
- Only affected nodes store state -- some shot at sharding?
- Notaries run the consensus, sign requests for guarantees.
	- Notary service running as a single node.

### Iroha - Sumeragi

- Heavily inspired by 'BChain' - Byzantine replication protocol that propagates transactions with a chain topology.
- Chain replication arranges nodes linearly with head and tail.
- The order of the nodes is based on a **reputation system** 
	- takes the age of the node and the performance.

### Kadena - Juno and ScalableBFT

- Juno from Kadena is a platform for running smart contracts.
- Claims to use Byzantine Fault Tolerant RAFT - similar model of ``f < n/3``.
- Well known from the literature that agremeent in consensus protocol can only be ensured with ``n > 3f``. 
- Replicating amongst the majority **will not suffice!**.

### Chain - Federated Consensus

- Chaina core is a generic infrastructure for institutional consortium to issue and transfer financial assets.
- Focuses on the financial services industry. 
- **Federated Consensus**
	- N nodes that make up network.
	- One node is static: *Block Generator*.
		- Periodically takes non-executed transaction and batches the request into block.
	- *Block Signers*: every signer validates the block proposed for the height - checking the signatures.
	- Protocol is resilient to a number of malicious *signers* but not a malicious *block generator*.
	- Overall:
		- Special case of BFT with a fixed leader.
		- Even if the generator crashes, protocol halts.
		- Protocol **cannot prevent forks** if the generator is malicious.
		- Since block generator must be correct - purpose of the signature issued by the signer is unclear.

### Quorum - QuorumChain and Raft

- QuorumChain developed by JPMorgan.
- RAFT-based consensus with QuorumChain.
- QuorumChain:
	- Smart contracts to validate blocks. 
	- Trust model: set of (n) voter node and some number of block maker nodes.
- Protocol is standard gossip based.
- Only block maker nodes permitted to propose blocks.
- Resilience: one block-maker node malicious = inconsistencies unless network is perfect.
- RAFT-based:
	- Second option = RAFT.
	- Replicate all transactions to participating nodes and ensure each node outputs the same sequence.

### MultiChain

- Dynamic permission model. 
- There is a list of permitted nodes in the network at all times.
	- Identified by pub keys.
- Derived from Bitcoin:
	- Don't solve computational puzzles.
	- Generate blocks after a random timeout.
	- Constraints = diversity parameter.
- Single attacking node generates transactions and blocks as it wants - if the network is favourable can essentially perform a 51pct. Attack.

### Further Platforms:

- **HydraChain**: adds support for creating permissioned blockchain using the Ethereum infrastructure.
- **Swirlds Hashgraph Algorithm**: protocol implemented in open-source consensus platform for distributed applications.o
	- Operates in a *completely asynchronous* mode. 
	- Hashgraph is randomized to circumvent FLP impossibility.

## Permissionless Blockchains

### Sawtooth Lake - Proof-of-Elapsed-Time (PoET)

- Hyperledger Sawtooth platform - running general purpose smart contracts on a distributed ledger.
- PoET:
	- Mandatory waiting time.
	- Nakamoto consensus participates in probabilistic experiment (but essentially is just a wait)
	- Consensus executes the waiting step inside the SGX.
	- No more mining required - but nodes rewarded for more hardware.

### Ripple and Stellar

- Ripple and Stellar are operating exchange networks operating with built-in cryptocurrencies.
- Depart from traditional security assumptions by making their trust assumptions flexible.
- Node would declare on its own which nodes it trusts.
- Each node designates a list of nodes - quorum slice. 
- Ripple:
	- Process of advancing ledger controlled by *validating nodes*. 
	- All pairs of validating nodes required - else fork.
	- Ripple states overlap = 1/5 * size of list. Peer review says otherwise.
	- Currently ripple provides default list of validators
		- Static config file.
	- Not decentralized.
- Stellar: 
	- Evolved from Ripple.
	- Each validator participates and declares it's own quorum slice of convincing trust nodes.
	- A node accepts a vote for the ledger when a threshold of nodes in the set confirm.
	- Similar structures well known with Byzantine Quorum Systems.

### IOTA Tangle

- Heralded as a cryptocurrency without a Blockchain.
- Creates a **HASH-DAG** called the Tangle. 
- All tokens created at the outset.
- Transactions propagated in P2P network - each transaction must be signed by owners key. 
- A transaction also includes PoW puzzle and a approves 2 or more transactions by including a hash of them in one. 
- A weight assigned to transaction proportional to difficulty of puzzle.
- Node is *supposed* to choose the tx randomly.
- A node can cheat by:
	1. Issuing invalid transactions (double spending)
	2. Invalid predecessor. 
	3. Not selecting the transactions to conform randomly. 
- How strongly a transaction approves of predecesor = weight.

## Conclusion

- Summary of blockchain consensus protocols.
- Argued that developing consensus protocols similar to engineering crypto protocols.
- Dangerous to entrust financial values to new protocols unless reviewed heavily.
- Overview of protocols and their properties contribute to a foundational understanding for formal protocol reviews and more technical comparisons.
- It will be interesting to compare their performance through benchmarks to observe their resiliance to actual attacks or network interference.


