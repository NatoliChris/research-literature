# Eclipse Attacks on Bitcoin's Peer-to-Peer Network

Ethan Heilman, Alison Kendler, Aviv Zohar, Sharon Goldberg

```
@inproceedings{heilman2015eclipse,
  title={Eclipse Attacks on Bitcoin's Peer-to-Peer Network.},
  author={Heilman, Ethan and Kendler, Alison and Zohar, Aviv}
}
```

---

## Summary

### Motivation

* Bitcoin has widespread adoption, relies heavily on Peer-to-Peer network.
* It has the assumption of perfect information.

### Problem

* Bitcoin client's connect to peers and rely heavily on synchronisation.
* Assumes some amount of correctness in the information received.

### Contribution

* Eclipse attack against nodes.
    * Isolation of nodes by filling peer table.
    * Splitting mining power.
    * Selfish mining.
    * Double spending.
* Analysis on Bitcoin's protocol.

---

## Introduction

* Bitcoin relies on a critical assumption on perfect information.
    * All members of the ecosystem can observe proofs-of-work from other peers.
* Most proposed attacks and countermeasures focus on the nodes or the computational proof.
* Not many look into the network and the core `bitcoind` implementation.
* Each bitcoin node uses a randomized protocol to select 8 peers.
    * These are long-lived *outgoing connections*
* Up to 117 **unsolicited** incoming connections from any IP address.
* Eclipse attack:
    * Attacker manipulates the incoming connections of the node.
    * Attacker controls an end host but not any critical infrastructure.
* Main challenge:
    * Sufficiently many IP addresses.
    * Possibilities: (1) infrastructure on ISP, or (2) botnets.
* **4600** bots have an 85% probability of successfully attacking.
    * Experiments show 400 bots sufficed in the live bitcoin node test.
* **Countermeasures**:
    * Two are typically recommended:
        1. Disable incoming connections.
            * How do new nodes join the network?
        2. Choose *specific* outgoing connections to well known miners.
            * How do we choose?
            * Will this form a kind of secondary chain / private network.
* Belief that decentralization is key - need to ensure that it will live up to promise.

### Implications of the attacks:

* **Engineering Block Races**:
    * Force forks to occur.
    * Wasted effort on the **orphaned** node.
* **Splitting Mining Power**:
    * Eclipsing fraction of miners will allow for lower overall network mining power.
    * Easier to perform the 51% attack.
 * **Selfish Mining**
    * Withold blocks from the eclipsed node.
    * Easier to selfish mine.
    * Filter any wanted blocks to eclipsed node.
* **0-Confirmation Double Spend**
    * 0-confirmation = merchant who believes transaction fulfilled as soon as it has been proposed.
    * Eclipsed merchant could be isolated so the transaction never makes it to the main chain.
* **N-Confirmation Double Spend**
    * Attacker sends transaction to the malicious miners
    * N-Blocks are mined by malicious miner in isolated network
    * Force the eclipsed merchant to believe the false blockchain.

## Bitcoin's Peer-to-Peer Network

* Peers are defined by the IP and placed into buckets.
* They are uniquely identified by the IP + Version of the client.
