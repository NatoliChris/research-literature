# Proof of Acitivty: Extendinig Bitcoin's Proof-of-Work via Proof of Stake

Iddo Bentov, Charles Lee, Alex Mizahi, Meni Rosenfeld

```
@article{bentov2014proof,
  title={Proof of Activity: Extending Bitcoin's Proof of Work via Proof of Stake [Extended Abstract] y},
  author={Bentov, Iddo and Lee, Charles and Mizrahi, Alex and Rosenfeld, Meni},
  journal={ACM SIGMETRICS Performance Evaluation Review},
  volume={42},
  number={3},
  pages={34--37},
  year={2014},
  publisher={ACM}
}
```

---

## Summary

### Movitvation

* Bitcoin faces a number of attacks;
* Want to provide a new system resilient to attacks but still same guarantees or better;
* Need a more robust protocol;
* Security based proof combining both PoW and PoS;


### Problem

* PoW is vulnerable to attacks;
* DoS is possible, Bitcoin is resilient but it relies on honest miners staying.
* After mining block reward has been removed, there needs to be some security with transaction fees.
* Other PoS proposals are impractical or face security vulnerabilities.

### Contribution

* Proof of Activity (PoA), combining PoS and PoW with strong security guarantees.
* Discussion of vulnerabilities of PoW and PoA, solutions to problems.
* Analysis of the security and cost differences.

---

## Introduction

* Bitcoin's release in 2009 revolutionised worldwide transactions.
* Many other features derived from Bitcoin's foundations.
* Attacks have been proposed on Bitcoin, which ones are likely to be practical?
* When Proof-of-Work is no longer rewarded with block reward, network needs a guarantee of reward by transaction fee.
* Purpose of PoA is to have decentralized cryptocurrency where security is based on combination of PoW and PoS.
* New centralization risks introduced with PoS.

## Motivation

* Different attacks that affect PoW.
* Double spending, DoS, majority hash power.
* DoS Case:
    * Attacker depletes resources when performing DoS.
    * Since honest miners will lose faith, attacker can have high chance of getting majority power.

### Attack Environment - Tragedy of Commons

* Likely that mining will become less lucrative when block reward is negligible.
    * Reward consists of only transaction fees.
* **Tragedy of Commons** - problem in economics and game theory.
    * System in which participants have the opportunity to act selfishly.
    * Selfish rational will always take the action because they are interested in their own well being.
    * If everyone acts selfishly, then everyone is worse off.
    * Occurs in Bitcoin.
* Without a way to force users to pay fees, vast majority of users will not pay.
    * Solution is that miners can choose to reject tx if the fee is low.
* Miners could form an agreement to accept only high-fee transactions.
    * Users forced to pay high fees.
    * Not stable/sustainable, miners will have to eventually accept low fees.
* Mining revenue is what funds the overall security.
* Having protocol-enforced rule (block value cap) will not be enough.
* Since the transaction fees are paying the miners who create the block, the miners will be incentivised not to propagate transactions so they benefit the most reward.

### Additional Hazards

* Network attacks and denial-of-service.
* Low connectivity between peers makes it easy for denial-of-service.
* Manipulation of the price of Bitcoin can make the confidence in the currency very low.

## Protocol

* Proof of activity is an extension on the current Bitcoin model.
* Network nodes need to do more complex verifications.
* Main part of Proof-of-Activity: *Follow-the-satoshi*.
    * Follow the chain of transactions for the single satioshi until the current user.
    * Random amongst the total number of satoshis in existence.
* **Proof-of-Activity Blocks**
    * Each miner generates an empty block header.
    * When miner succeeds, broadcast header to network.
    * All nodes regard hash as data that derives the stakeholders - the *follow-the-satoshi* begins.
    * Nth stakeholder broadcasts wrapped blocks to the network - following along.
    * Once the final has been called, the fees are collected and rewarded to the stakeholders and miner.

### Discussion of the Protocol

* N stakeholders offline, other miners will also solve the block and pick new subset of N.
* Block creation accomplished in two rounds
    * Picking lucky stakeholders measures the Hamming distance between pseudorandom value and valid address.
* Still requires some of the PoW component to determine the subset of nodes to complete.
* Rational stakeholders can maximize their expected reward by signing every forked branch that they see.
* Protocol can allow N pseudorandom stakeholders to move coins into a new address instead of sending an auxiliary signature.
* The word activity emphasizes the point that only active stakeholders who obtain a "full online" presence get the reward.
* The Proof-of-Activity protocol **rewards** the stakeholders who participate and sustain the network.
    * Rather than **punishing** the passive stakeholders.
* *The rich get richer* could be seen as a scenario that occurs.
    * Paper Extract: "Though taken to the extreme, the stakeholder's coins offer her no value unless she spends them."
* PoA nodes might have no incentive to forward data for the blocks being created to gain the most reward.
    * I.e. selfish mining.

### Add-ons to the protocol

#### Incentivising a target participation level

* An empty block shows the nodes that have verified the hash of the block.
* At each difficulty re-target, the nodes sum up the amount of empty blocks generated since the last re-target.
    * Relative reward of the stakeholders should increase during the window if their participation level is too low.
* To encourage stakeholders to include extra empty blocks, the stakeholder who creates the block receives small fees.

#### Discouraging thin clients by Proof-of-UTXO

* One way to combat malicious miners is to force them to prove capable of validating transactions through Proof-of-UTXO.
* By extending the PoW to be memory bound - the hash is derived from the UTXO table.
* Greedy stakeholders that do not maintain a set of UTXOs only take the transactions and participate in *follow the satoshi*
* The PoA protocol can force clients to have the set of UTXOs.
* This form of UTXO is energy efficient as the required PoW difficulty can be very mild.

#### Limited withdrawal key delegation

* The node online has to sign with the private key - if the node is compromised then the attacker gains access to the private key.
    * By contrast, PoW miners are not exposed to this risk.
* Eliminating the risk:
    * Let stakeholders delegate their signing power to special keys that are allowed to sign when the lottery is won.
    * Private keys can be kept in encrypted form as the online nodes participating in the lottery do not utilise them.
* Middle-of-the-road approach where the delegated key can be used periodically to transfer limited amounts of coins to any address.
* The coins transferred would have to wait a certain time (lock-time) until they are used.
    * **lock-time** = ``h + T`` where `h` is the current height of the chain, T is the period to wait.

#### Decentralized Stake Pools

* Protocol can support a contract that splits reward amongst a pool of stakeholders.
* It could still be the case that only one of the pool nodes is online, communicating to the other participants who maintain an online presence to provide signatures when needed.
* Due to the overhead, this kind of stake pool may not be suitable for small stakeholders.


## Money Supply

* Pure Proof-of-Stake protocols, distributing the coins is less straightforward.
* Proof-of-Attack benefits the PoW aspect that is incorporated into the system.
* If the PoA protocol specifies that the block reward is divided equally between the miner an N lucky nodes, it may be likely to unfairly make the rich richer.
* One alternative is to use pure PoW until the first block reward halving (4 years) and then switch to PoA.

## Security

* PoA protocol vests some of the power is in the hands of the stakeholders.
    * An attacker needs to control a large portion of the stake if they want to double spend with a hidden branch.
* Letting the lucky node determine the transactions included in the block provides security against an attacker trying to deny transactions.
* The larger N is, the possibility for a bribe attack becomes tangible.
    * The attacker can try to communicate with online nodes (differentiating between the online and offline)
    * Increases the risk of the attack being made public.
* PoW-based cryptocurrency, the security is sustained under the assumption that the majority of the mining power participating is honest.
* Security of PoA deteriorates quickly when the majority of the online stake is under the control of a malicious entity.
    * Assumption is that the majority of the stake is honest.

### Network denial of service

* Network is resistant to DoS attackers who broadcast many of the invalid blocks, since it is easier to verify whether the wrapped block contain the public key.
* In the case the Nth lucky stakeholder is an attacker, the nodes can easily resist the DoS by blacklisting the block with this Nth stakeholder.

### Centralized Stake Pools

* One potential problem is the centralization of the proof-of-stake pools.
    * Stakeholder might be inclined to join a pool to decrease expected time and variance to collecting reward.
    * Inexperienced users may assume that the online wallet can secure a small amount of coins.
    * Stakeholder will trade services for a portion of the reward.
* Risk taken by hosting online wallet is higher than PoW.
    * Risk losing all of the money.
    * We cannot have a pool operating in the same way that the PoW pools operate.
    * The pool's reward address (coinbase) cannot be set in advance.

### Double Spending Bribe Service.

* Centralized entity providing a double-spending service.
* Entity runs a stealth operation letting stakeholders collude.
* Service would share the profits obtained by double-spending.
* Clear distinction between PoS and PoW for this job:
    * Miners will waste their resources if PoW double spend fails.
    * Collusion between stakeholders should be secretive.
    * If the entity openly advertises, merchants could listen and reject any sales from the known service.
    * If the centralized service is public, it is vulnerable to DoS.
* In order to maintain a double spending, the service must keep the majority of the block creating power over a potentially long period of time.
    * This is infeasible if the services were distributed
    * Unlikely because stakeholders do not wish to have stake diminish in value if the double-spend fails.

## Cost Analysis

* Antminer S2 runs at terahash - costing 1 coin.
    * Total number of hashes is 50k terahashes per second.
    * Attacker needs 50,000 antminers (400,000 coins) in order to have half the mining power.
* If the total hashrate needed in PoA is 1/10 of the power.
    * Attacker needs 40,000 antminers (320,000 coins) in order to be 8 times faster than honest miners in PoA.
    * Cost of gaining an advantage over the honest network.

## Conclusion

* PoA decentralizes power.
* Monopolizing block creation - an attacker needs to control a substantial fraction of the coins.
    * Cost of attack much higher than that of Bitcoin's PoW alone.
* Accomplishes beneficial properties, network topologies, for maintaining online nodes.
* More efficient energy use and lower transaction fees.
