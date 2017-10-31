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

* Sucess probability increases with the percentage of IPs in the `tried` table.
* Victim is eclipsed if all eight outgoing connections are adversarial addresses.
    * Equals ``8 / (64*64)``

**Random Selection**

* Without random selection - probability is 90% even if it fills 72% of the table
* With random selection of peers in the table, 90% success requires 98.7% of the `tried` table to be attacker addresses.

### Monopolizing the Eclipsed Victim

* Often assumed that the number of TCP connections a computer can make is limited by the OS.
    * The limit only applies to OS-provided TCP sockets.
    * Attacker is able to arbitrarily open any TCP sockets.
* **Attacker Connections**
    * Filling `tried` table requires connection to victim from each address.
    * Each connection has ``VERSION + Handshake`` and disconnects via ``TCP RST``
    * Some connections also send one ``ADDR`` message.
* **Monopolizing Connections**
    * If attacks succeeds, victim has 8 outgoing to the attacker.
    * TCP connections can be maintained **up to 30 days**, in which the victim's address is then `terrible`.
    * TCP connections are maintained by ping-pong packets and `TCP ACK`.
    * *Aside*:
        * This also allows for connection starvation attacks, monopolizes all incoming connections making it impossible for new nodes to connect.

## How Many Attack Addresses

* Probabilistic Analysis to determine the number of addresses.
* Important to remember that if the number is small, our attacker can still succeed.
* Two variants:
    * Botnet Attack: several IP addresses in distinct group. Scattered IP addresses.
    * Infrastructure Attack: Controls several IP address blocks 

### Botnet

* **Initially Empty** Best case for the attacker - all 64 buckets are empty.
* **Bitcoin Eviction**
    * Bucket is full of 64 legitimate addresses.
    * Since the bitcoin eviction discipline requires *each newly inserted address select 4 random addresses* in the bucket to evict, legitimate addresses will be overwritten by the adversarial address.
* **Random Eviction**
    * Attacker's worst case = 64 legitimate addresses.
    * Assume each address is inserted and evicts random address.
* **Exploiting multiple rounds**
    * Attack proceeds in rounds.
    * Number of rounds move IPs into buckets of `tried` table.
    * May increase over time as the number of IPs may hold for future rounds.

#### Who can launch a botnet attack?

* Initially empty - a botnet with **6000** addresses can fill the `tried` table.
* *Carna Botnet*:
    * 1250 bots
    * 402 distinct groups
    * Enough to attack live bitcoin nodes.

### Infrastructure Attack

* Attacker holds addresses in `s` distinct groups.
* Determine how much of `tried` can be filled by an attacker controlling `s` groups containing `t` IP addresses.
* How many groups?
    * 55.5 out of 64 buckets with ``s=32`` and all but one bucket with ``s > 67``.
* How full is the tried table?
    * Adversaries target easiest when all buckets initially empty OR when sufficient number of rounds used.
    * A single `/24` address block of 256 addresses suffices to fill each bucket when ``s=32``.
    * Attack exploiting rounds
        * 32 groups of 256 addresses in each (`total=8192`) can expect to fill **86%** of the `tried` table.

#### Who can launch the attack

* There are **448** organisations with ``s=32`` groups and at least ``t=256`` addresses per group.
* National ISPs in various countries hold a sufficient number of groups.

### Infrastructure or Botnet?

* Filling 98% of the `tried` table requires a 4600 node botnet (with sufficient rounds).
* Contrasting, an infrastructure attacker need 16000 addresses with ``s=63`` groups and ``t=256``.
* Context:
    * If 3000 bots joined today's network (with ``<7200`` public IP nodes) and honestly followed the peer-to-peer protocol.
    * Could eclipse a victim with **0.006%** probability.

## Live measurements

* Analyzed typical bitcoin nodes, implemented 5 nodes.
* Number of connections:
    * Attack requires the victim to have available slots for incoming connections.
    * Can get 125 peers, never see more than 60 at a time.
    * 80% of Bitcoin peers allow at least 40 incoming ocnnections.
    * On average: 9.9 connections to public IPs over the course of a lifetime; 
        * 8 correspond to outgoing connections.
* Connection Length
    * Many connections were long lived.
    * Bitcoin's core does not drop outgoing connections (unless reasons like network dropout or blacklisting etc.)
    * 17% of connections lasted more than 15 days.
    * 65.5% of these were public IPs.
    * Bitcoin nodes restart frequently - 43% of the nodes lasted **less than 2 days**.
* Size of tables
    * Worst case attack assumed that the tables completely full.
    * Seen: tables were **far from full of fresh nodes**.
    * 43 days - `tried` tables were no more than ``300/4096`` full.
        * Means most were successful outgoing connections never taken from `new`.
* Freshness of `tried`
    * Few addresses not especially fresh.
    * 17% of addresses were more than 30 days old.
    * 48% were more than 10 days old.
    * Note: will be less preferred than adversarial nodes (increasing probability of successful attack)
* Churn
    * Small fraction of addresses in `tried` were online when connecting at the end.
    * Further suggests vulnerability to eclipse attack.
        * Most legitimate addresses are offline when reset - victim likely to connect to adversary.

## Experiments

* Methodology
    * Victim running a public node on the public mainchain.
    * Attacker can read all victim's packets to/from public Bitcoin network.
    * Begin attack: Attacker forges TCP connections from arbitrary IP addresses
    * Avoid flooding bitcoin network: used the `reserved` IPs as attack addresses and trash `ADDR` addresses.
    * Drop any `ADDR` messages that were polluted from the Bitcoin node.
    * Repeatedly restart the victim and see what outgoing connections are made.
    * Each experiment restarts the victim 50 times.
* Initial Conditions
    * Worst Case
        * `tried` and `new` tables completely full.
        * All legitimate addresses no older than an hour.
    * Transplant case
        * Copied the `tried` and `new` table from live bitcoin nodes.
        * Restarted the victim waited to connect.
    * Live case
        * Attacked their own live Bitcoin node.
* **Observation of Results**
    * Success in worst case:
        * Experiments confirm that infrastructure attack succeeds with high probability.
        * Botnet (4600 bots) had **100%** success.
    * Accuracy of Predictions
        * Experiments highlight that there is **higher success than predicted**.
        * Experiments dramatically more successful: Botnet 88% success, 34% prediction.
        * Explanation looks at the `new` table - assume `new` is completely overwritten, infrastructure attacks failed to completely overwrite entire table - having more failures than botnet.
    * Success in *typical* case.
        * Small botnet (400 bots) have a **very high** probability.
        * 400 bots have ``400/650`` filled address which is 62% of tried.
        * Manages to win **80%** of the time.

## Countermeasures

* If the victim has legitimate addresses in the `tried` before the attack starts:
    * During attack when victim restarts, even the attacker (with **unbounded** number of addresses) can not eclipse with high probability.
* Deterministic Random Eviction
    * Each node hashes to a particular spot in the buckets (deterministic)
    * No repetition of same address in the bucket.
* Random Selection
    * Attacks exploit heavy bias towards outgoing connections.
    * Eliminate advantage of attacker if addresses selected at random from `tried` and `new`.
    * This increases the difficulty, but still is possible.
* Test before evict:
    * Try to connect to the old node, if still active then no evict.
    * Bounded probability of eclipsing the node.
    * Performed **Monte-Carlo** simulation (Random sampling used to measure probability)
    * Test before evict bounds the attacker (even with infinite bots) to 50%.
* Feeler Connections
    * Short-lived test connections to randomly selected addresses in `new`.
        * If success, address moved to `tried`.
        * Feelers can clean the *trash* from the `new` table.
* Anchor Connections
    * Two connections that persist on restart.
    * Inspired by ToR.
* More buckets
    * Increase the number of buckets rapidly increases the number of addresses.
    * Infrastructure attack needs **double** the amount of groups.
    * Botnet needs to double the number of bots.
    * Only helpful when `tried` already contains legitimate nodes in the buckets.
* More Outgoing Connections
    * 64 connection slots available (80% of Bitcoin peers allow at least 40 incoming)
    * Require nodes to make more outgoing connections without risking that network will run out of connection capacity.
    * Example: using 12 outgoing instead of 8 decreases the probability.
        * Infrastructure attack (for 50% success probability) will need 46 **groups**.
        * Botnets need 11796 bots!
* Ban unsolicited `ADDR` messages
    * Don't accept large ADDR messages (``>10`` addresses).
    * Prevents adversary from flooding the peer.
* Diversify Incoming connections
    * Before fixes: All incoming can be from the **same IP address**
    * After: Different IPs.
* Anomaly Detection
    * Several *signatures* that can be detected
        1. Flurry of short-lived incoming TCP connections.
        2. Large `ADDR` messages.
        3. Trash IP addresses.
    * Attacker suddenly connecting can be detected.

**Status of Countermeasures**

* Deployed in `bitcoind v0.10.1`.
* Now uses deterministic random eviction, random selection and scales up the number of buckets.
* Botnets require 163000 addresses for a 50% success, 248000 for 90% success.

## Related Work

* Peer-to-peer network
    * Recent work looking at delaying block propagation.
    * Works discuss aspects of networking protocol and `ADDR` messages.
* P2P and Botnet Architectures
    * Extensive research into Eclipse attacks in other fields.
    * Many proposals to defend against.
    * Bitcoin uses unstructured network.
        * Focused on specific quirks in Bitcoin's network.
* ``Rossow et al - SoK: P2pwned-modelling ad evaluating resilience of peer-to-peer botnets.``
    * Many botnets use similar unstructured p2p networks.
    * Gossip messages.
    * Describes how botnets defend against attacks that flood information tables.

## Conclusion

* Eclipse attack on Bitcoin's p2p network.
    * 0/N confirmation double spending
    * Adversarial forks
* Demonstrated practicality in which:
    * Botnet of 4600 nodes has 85% probability.
    * Botnet of 400 also relatively high probability.
* Proposed countermeasures.
