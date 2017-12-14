# BitIodine: Extracting Intelligence from the Bitcoin Network

```
@inproceedings{spagnuolo2014bitiodine,
  title={Bitiodine: Extracting intelligence from the bitcoin network},
  author={Spagnuolo, Michele and Maggi, Federico and Zanero, Stefano},
  booktitle={International Conference on Financial Cryptography and Data Security},
  pages={457--468},
  year={2014},
  organization={Springer}
}
```

---

## Summary

### Motivation:

* Bitcoin transactions don't explicitly identify their payees
* Information can be used for forensics and illegal activity.


### Problem:

* Bitcoin addresses pseudononymous, but can be owned by one person. No way to explicitly see who transactions are going to.
* There is a high level of illegal activity that occurs through Bitcoin.

### Contribution:

* BitIodine framework
    * Graph of transactions
    * Cluster and identification of addresses.

---

## Abstract

* Data is difficult to analyse, most of it has been performed manually.
* The work is to identify addresses of value.

## Introduction

* Bitcoin - decentralized P2P system that validates and certifies transactions.
* There is a cryptographic guarantee of each transaction.
* Each node stores the entire history of the chain.
* Some addresses of the exchanges/etc are well known, but a majority aren't.
* Addresses can be clustered.
    * Previously it was manually labelled.
* Method:
    * Web scraper scrapes important sites and labels and clusters.
    * Place into feature oriented database allowing fast queries about addresses.
* Framework will have little to no supervised clustering.

## State of the Art and Motivation

* No explicit was to identify payee or payers of transactions.
* Potentially interesting information can be mined through the blockchain since it is all transparent.
* Can find addresses for gambling and scamming and see how much volume they have seen.
* Previous work looks at privacy provisions and data mining and anomaly detection.
* Activity of known users can be seen using passive analysis only.
* Obfuscators occur to increase user anonymity.

## System Design

* **Block Parser**: reads the local blockchain database and exports into a queryable database (e.g. SQLite).
* **Clusterizer**: locates groups of addresses belonging to same user (through heuristics).
* **Scrapers**: scrape the web to find addresses associated to real users.
    * *Usernames*: BitocinTalk and Bitcoin-OTC marketplace.
    * *Physical Coins*: Casascius along with their Bitcoin value and status.
    * *Scammers*: identifying users that have significant negative feedback on Bitcoin-OTC.
    * *Shareholders*: in stock exchanges.
* **Grapher**: Incrementally reads the blockchain database and cluster file to create a **transaction** and **user** graph.
* **Classifier**: Reads the transaction and user graph and automatically labels the addresses and clusters with notations.
* **Exporter**: Export and filter (portions of) the transaction graph and user graph.
    * Allows exporting transactions that occurred within a cluster.

### Algorithms and Analysis Approaches

* Blocks are uniquely identified by indexes starting from 0.
* Transactions with unique indx, sender and receiver.
* **First Heuristic - Multi-input transaction grouping**
    * (NOTE FROM ME: This is invalidated due to the multi-sig wallets)
    * Assume that transactioins with inputs from multiple addresses all belong to the same wallet and user.
* **Second Heuristic - Shadow Address Guessing**
    * Shadow addresses are used to keep change.
    * Prediction of which addresses belong to the *owner* of the transaction indicating the change.
    * *deterministic and conservative*:
        * If there are two outputs, one address seen in chain, other not - the shadow is the one that has not appeared.
    * Bug in the wallet (now patched) made it able to deduce this due to a random function.

### Implementation Details

* Several gigabytes of data and graphs with millions of nodes.
* Block Parser - **C++**.
* All other modules - Python 3.3.3
* Database exported to **SQLITE**.
* The clusterizer designed to be incremental.
    * Pause the generation of clusters.
    * Embed arbitrary number of nodes/edges.

## Experimental Case Studies

### Dread Pirate Roberts - Silk Road Ross Ulbricht.

* Believed to be owner of silk road.
* Seized 29.6K BTC in hot wallet, 144K in cold wallet.
* Using BitIodine.
    * Known address of DPR.
    * DPR used name *altoid* on BitcoinTalk forum.
    * Posted asking questions of PHP and known as lead developer for startup.
    * Pasted one of the addresses.
    * BitIodine found that the known address belonged to cluster of 6.
    * One was the DPR address.
* Interesting points:
    * Every address has the first input coming from previous one in the chain.
    * Manual mixing and obfuscation.

## Performance Evaluation

* Clusterizer generates large number of clusters.
* Scalability issues rise with a larger blockchain.
* SQLite database holds all info in memory - will be issue.

## Future Work

* Limited by the first heuristic, works under assumption of no shared private key.
* Current implementation of the classifier needs to load transaction graph AND clusters into memory.
* Current label is semi-auto by scraping information of known addresses. Future work = behavioural patterns of users/etc.

## Conclusion

* BitIodine is a modular framework for doing forensics on Bitcoin addresses.
* Can analyse the web and perform semi-automated tagging.
* Useful for the FBI or investigations into addresses.
