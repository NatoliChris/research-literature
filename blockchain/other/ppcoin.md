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


