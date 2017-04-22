# Bitcoins the hard way: Using raw bitcoin protocol

URL: [http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html](http://www.righto.com/2014/02/bitcoins-hard-way-using-raw-bitcoin.html)

Accessed: 30/03/2017

## Overview of Bitcoins/Blockchain

### Transactions

- Owner transfers an amount of currency to an address.
- Key innovation
	- All transactions logged into a decentralized log = blockchain.
	- The blockchain is built of *blocks*.
	- *Miners* are specialized nodes that add to the blocks by solving a crypto puzzle known as **Proof-of-Work**.
### P2P Network

- Completely decentralized network, all information is passed from node to node.
- Uses a *gossip* protocol, each node will forward on information that it received.

### Crypto

- Uses digital signatures to sign transactions.
- Owner of a bitcoin address has a private key associated.
- Uses 256-bit cryptographic hash of contents to identify transactions.

## Bitcoin Addresses and Keys

- 256bit private key -> ECC creates a 512-bit public key.
- Public key used to verify transactions.
- Bitcoin has a [Base58Check](https://en.bitcoin.it/wiki/Base58Check_encoding).
- Types of Keys (summary):
	- Private Key (256 bit).
	- Public Key (512 bit generated from ECC of priv).
	- Hash of the public key - Base58Check.
	- Address = SHA256+RIMEMD-160 then BASE58CHECK.

## Inside a transaction

- A bitcoin transaction moves **inputs** and **outputs**.
	- Each input is a transaction + address.
	- Each output is an address and bitcoins going to address.
- Each input must be entirely spent in a transaction.
	- All leftover/unspent will be a second transaction back to the owner.
	- This is what UTXO comes into play for.
- Transactions also include a *fee* such that the miner gets paid.
- Specification from bitcoin: [https://en.bitcoin.it/wiki/Protocol_specification#tx](https://en.bitcoin.it/wiki/Protocol_specification#tx)

## How bitcoin transactions are signed

- Public key must correspond to address of node.
	- Note: public key is never made public until a transaction.
- Signature on the transaction can be verified using the public key.
