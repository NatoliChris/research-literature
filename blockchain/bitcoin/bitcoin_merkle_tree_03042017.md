# Merkle Trees and Merkle Blocks

Citations: 

```

@Inbook{Merkle1988,
author="Merkle, Ralph C.",
editor="Pomerance, Carl",
title="A Digital Signature Based on a Conventional Encryption Function",
bookTitle="Advances in Cryptology --- CRYPTO '87: Proceedings",
year="1988",
publisher="Springer Berlin Heidelberg",
address="Berlin, Heidelberg",
pages="369--378",
isbn="978-3-540-48184-3",
doi="10.1007/3-540-48184-2_32",
url="http://dx.doi.org/10.1007/3-540-48184-2_32"
}

@Inbook{Szydlo2004,
author="Szydlo, Michael",
editor="Cachin, Christian
and Camenisch, Jan L.",
title="Merkle Tree Traversal in Log Space and Time",
bookTitle="Advances in Cryptology - EUROCRYPT 2004: International Conference on the Theory and Applications of Cryptographic Techniques, Interlaken, Switzerland, May 2-6, 2004. Proceedings",
year="2004",
publisher="Springer Berlin Heidelberg",
address="Berlin, Heidelberg",
pages="541--554",
isbn="978-3-540-24676-3",
doi="10.1007/978-3-540-24676-3_32",
url="http://dx.doi.org/10.1007/978-3-540-24676-3_32"
}

```

URLs:

- [https://bitcoin.org/en/developer-reference#merkle-trees](https://bitcoin.org/en/developer-reference#merkle-trees)
- [https://bitcoin.org/en/developer-reference#merkleblock](https://bitcoin.org/en/developer-reference#merkleblock)


## Merkle Tree

![merkle construction](https://bitcoin.org/img/dev/en-merkle-tree-construction.svg)


- Merkle Root = Constructed with SHA256 hash of transactions.
- **Coinbase Transaction**: first transaction in a block, always miner - single coinbase.
- Cases:
	- Only coinbase transaction : this is merkle root.
	- Coinbase + 1 : placed in order and concatenated ``SHA256(SHA256())``.
	- Coinbase + >= 2: Form a tree
		- transactions placed in order, paired and hashed.
		- If odd number, one transaction may be paired by itself.
		- Traverse up to get a merke root
- All in **internal byte order** = hash digests displayed as string.

## Merkle Block

- ``merkleblock`` is a message that is returned when a block is requested.
- Parsing the `merkleblock` message:
	- Contains three data types:
		- Transaction Count
		- List of hashes
		- List of one-bit flags
	- Reconstruct an empty merkle tree using the transaction count
	- Bottom = TXID nodes, all others are non-txid
	- Using the bits follow:
		- 0
			- TXID = use next hash as this nodes TXID; this transaction didn't match the filter.
			- Non = Use next hash as this node's hash, don't process any descendants
		- 1
			- TXID = use next hash as this node's TXID mark this transaction as matching the filter.
			- Non = Hash needs to be computed - process left and right to generate the hash - concat and generate 64 byte hash.
	- Perform the lookup **DEPTH FIRST** until you reach a TXID node or non-TXID node with 0 flag.
	- Once you reach this point - ascend the tree.

