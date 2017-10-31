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
    * Attacker sends transaction to the malicious miners.
    * N-Blocks are mined by malicious miner in isolated network.
    * Force the eclipsed merchant to believe the false blockchain.

## Bitcoin's Peer-to-Peer Network

* Peers are defined by the IP and placed into buckets.
* They are uniquely identified by the IP + Version of the client.

### Propagating Network Information

* Root DNS seeds are hardcoded into the Bitcoin client.
    * Seeds periodically crawl the network to update their IP tables (knowledge of all nodes).
    * Only 6 seeds periodically queried in two cases:
        1. It is a new node connecting to the first time, connects to seeders for list of IPs or fials to a hardcoded list of 600 IPs.
        2. Existing node restarts and connects to peers; if failed to connect - queries the hardcoded DNS seed.
            * Only queries the seed if no connections for **11 seconds**.
* `ADDR` Message:
    * Up to 1000  IP addresses and timestamp from other nodes.
    * Nodes accept unsolicited `ADDR` messages.
    * Nodes push `ADDR` messages in two cases:
        1. Each day node sends all known IP addresses in `ADDR`.
        2. Node receives `ADDR` with `<10` addresses, forwards on to two randomly selected peers.

### Storing Network Information

* Two tables:
    * **tried**:
        * 64 buckets, 64 IPs.
        * All nodes that have successful connections.
    * **new**:
        * New IPs that the node has not connected to.
        * Contains all IPs that the node knows about.
        * 256 buckets, 64 addresses.
* Buckets = hash of the IP.
* Group = `/16` IPv4 prefix.
* When a node successfully connects to a peer, adds to the `tried` table.
* If the bucket is full, starts the *eviction* process.
    * Oldest IP (timestamp) is replaced with new peer's address in `tried` table.
    * Old peer is then inserted into the `new` table.
* If the peer's address is already present in the bucket, the timestamp is updated.
* If a bucket is full, then the `isTerrible` function run on every address.
    * More than 30 days old.
    * Has had too many failed connection attempts.

### Selecting Peers

* A Bitcoin node **never deliberately drops** connections.
* Only exception is whe blacklisting a node.
    * Example: `ADDR` message greater than 1000 IPs.
* Selecting peers:
    1. Decide whether to select a peer from `tried` or `new`.
    2. Select an address from random with a bias towards fresher addresses.
        1. Choose a random non-empty bucket from table.
        2. Choose a random position in that bucket.
        3. If there is an address in that position, return the address (with probability)
    3. Connect to the address, if connection fails - go to 1.

## The Eclipse Attack

* Attacker:
    1. Populates the `tried` table with addresses for it's attack nodes.
    2. Overwrites addresses in the `new` table with "trash" IP addresses.
* The attack continues until the victim node restarts and chooses new outgoing connections.
* The attacker occupies the victim's remaining 117 incoming connections - it is now eclipsed.

### Populating the `tried` and `new`

1. Unsolicited Incoming:
    * Addresses from unsolicited incoming connections stored in `tried` table.
    * Bitcoin's eviction discipline means attacker fresh addresses likely to evict older ligitimate addresses.
2. Unsolicited `ADDR`
    * Node accepts unsolicited `ADDR` messages.
    * Attacker connects to victim and sends `ADDR` with trash.
3. Nodes rarely solicit network from DNS
    * While attacker overwrites tables, victim never counteracts.

### Restarting the Victim

* Several reasons why a bitcoin node would restart.
    * ISP outages.
    * Power failures.
    * Upgrades.
    * Attacks/Fails on the OS.
* Node with a public IP has a **25% chance** of going offline after **10 hours**.
* Since updating is often not optional, especially when it corresponds to critical security.
* Attacker could also be via DDoS, memory exhaustion or packets-of-death (found for bitcoind).

### Selecting Outgoing Connections

* Attack succeeds **if and only if** ***ALL*** outgoing connections are to the attacker.
* To achieve this, the attacker utilises the bias towards the fresh timestamps from the `tried` table.
* Assumptions:
    1. Fraction of addresses in victim's `tried` table are controlled by adversary, remaining fraction are legitimate
    2. All addresses in the `new` table are *trash*.
    3. Attack proceeds in rounds, repeating each round until victim restarts.
        * Each round, the attacker connects the victim to adversarial IP address.
        * All adversarial addresses in `tried` must be younger than the length of the round.
    4. A fraction (`f'`) of addresses in `tried` are actively connected to the victim before restart.
    5. Time invested in the attack is the time elapsed since adversary starts the attack to the point at which the victim restarts.

**Success Probability**
