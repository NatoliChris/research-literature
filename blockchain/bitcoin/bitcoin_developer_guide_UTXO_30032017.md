# Bitcoin Developer Guide (UTXO only)

URL: [https://bitcoin.org/en/developer-guide](https://bitcoin.org/en/developer-guide)

Accessed: 30/03/2017

## Unspent Transaction Output (UTXO)

- Transactions have **inputs** and **outputs**
- Each transaction can only be spent once, the output can be categorized as:
	- **Spent** transaction outputs (normal).
	- **Unspent Transaction Output (UTXO)** which is money that is in a way the change from a transaction.
- The UTXO of a coinbase cannot be spent for at least 100 blocks.
	- Prevents double spending from a miner as well.
- The users **BALANCE** is just how much currency they hold of *UTXOs*.
- The UTXOs are kept in a database.
### UTXO and Change

- Change output is surplus of UTXO from a transaction being returned.
- It is mentioned that it is good practice that change output be sent to a new address.

## Verification of Payments

- Only one transaction can spend one entire input - it is illegal to have two transactions with the same input.
- For each block, the transaction gains **1 confidence**.
	- The higher the confirmation, higher protection/confidence it is in the chain.

| Confirmations | Description                                               |
|---------------|-----------------------------------------------------------|
| 0             | Transaction has been broadcast but not confirmed.         |
| 1             | Transaction is in a block.                                |
| 2             | There is one block above the block the transaction is in. |
| 6             | Network spent an hour working to protect - 5 blocks above |



