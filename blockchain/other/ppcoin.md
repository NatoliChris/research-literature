# PPCoin: Peer-to-Peer Crypto-Currency with Proof-of-Stake

```
@article{king2012ppcoin,
  title={Ppcoin: Peer-to-peer crypto-currency with proof-of-stake},
  author={King, Sunny and Nadal, Scott},
  journal={self-published paper, August},
  volume={19},
  year={2012}
}
```

---

## Summary

### Motivation

- Proof-of-work has been backbone of minting.
- Proof-of-work has limitations, need something energy efficient.

### Problem

- Proof-of-work highly adapted, but not very energy efficient.
- Security of system dependent on long term.

### Solution

- Proof-of-stake based on coin **age**.
- Formalized design where proof-of-stake used to build security model in cryptocurrency.

---

## Introduction

* Proof-of-work is the backbone of minting and security model.
* Concept of *coin age* can facilitate alternative design.
* Design attempts to demonstrate viability of future peer-to-peer cryptocurrencies not dependent on energy consumption.

## Coin Age

* Concept known to Nakamoto, used in Bitcoin to help prioritise transactions.
* Coin age defined as `` currency amount * time holding period ``.
* Coin age destroyed when transferred.
* Introduced a timestamped field with transactions.

## Proof-of-stake

* Proof-of-work is a major breakthrough, but is dependent on energy consumption.
    * Significant overhead of operation.
    * Mining rate slows, could put pressure on raising transaction fees to sustain security level.
* Concept termed *Proof-of-Stake* from bitcoin community (2011).
* Proof-of-stake and coin age can replace most proof-of-work as holds same security models.

## Block Generation

* Hybrid design, blocks separated into two types.
    * PoW blocks
    * PoStake blocks:
        * Special transaction called **coinstake**: owner pays himself, consuming coin age, gaining privilege to generate block.
* The first input of coinstake is the ``kernel`` and is required to meet certain hash target protocol. This is the proof-of-work. 
* Important difference is that hashing operation done over limited search space.
* Hash target is a target per unit coin age (coin-day) consumed.
* Proof-of-work hash target and proof-of-stake target adjusted continuously rather than Bitcoin's two-week rule.

## Minting based on Proof-of-Stake

* New mining process in addition to the proof-of-work minting.
* Proof-of-stake block based on coin age consumed.
* 1 cent per coin-year.
* Why PoW?
    * Facilitates initial minting of coins.
    * Else - initial minting as part of the genesis block / IPO.

## Main Chain Protocol

* Chain with highest coin age chosen as main chain.
* Alleviates concerns of 51% assumption.
    * First the cost of controlling stake might be higher than acquiring mining power.
    * Attacker's coin age is consumed during the attack.

## Checkpoint: Protection of History

* Disadvantages of using total consumed coin age to determine main chain is that lowers cost of attack on entire chain history.
    * Nakamoto introduced checkpoints in 2010.
    * This prevents any changes to history.
* Cost of double-spending may have been lowered - attacker only needs to accumulate coin age and force reorganisation.
    * Additional form of checkpoints broadcast centrally at smaller intervals to freeze and finalize transactions.
* Bitcoin doesn't solve the distributed consensus problem for checkpointing.
    * Centralization kept until complete distributed approach has been developed.
* Use of central checkpointing:
    * Defending against type of DoS, kernel must be verified before PoStake block.
    * Deadline of checkpointing needed to capture all node's capability of verifying connection.
    * Coin age computation requires minimum age.
    * Central checkpointing used to ensure all nodes agree on past transactions.

## Block Signatures and Duplicate Stake Protocol

* Each block signed by owner to prevent stake being copied and used.
* Duplicate-stake protocol designed to defend against attacker.
    * Each node collects ``<kernel, timestamp>`` pair of coinstake transactions.
    * If received block contains duplicate pair as previously received block.

## Energy Efficiency

* When PoW mint rate approaches 0, less and less incentive to mine PoW blocks.
* Long term energy-efficient scheme is all miners transfer to Proof-of-Stake minting.

## Other Considerations

* Modified proof-of-work mint rate to not be determined by block height, but difficulty instead.
    * Difficulty goes up, mint rate goes down.
    * 16X raise in difficulty = half block mint amount.
* Effect of transaction fee as incentive to not cooperate.
    * In PPCoin, no more fees to block owner, so incentive is lost.
    * Removes the incentive to not acknowledge other miner's blocks.
* Enforce transaction fees at protocol level to defend against bloating attacks.
* **Proof-of-Excellence** 
    * Tournament held periodically to mint coins based on the performance of the tournament participants.
    * Provides intelligent form of energy consumption.

