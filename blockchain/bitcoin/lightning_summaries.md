# Lightning Network

## Summary Doc

* First "smart-contract" using Bitcoin Scripting.
* Lightning payments do not need confirmations.
* Allow for micropayments (1*e-8 Bitcoin) where as main transactions have mininmum much higher.
* Higher transaction volume:
    * Machine-to-machine payment.
    * Off-chain with no delegated trust or ownership.
* How?
    * Multi-sig 'Channel' - on chain backbone.
    * To exit - both parties agree on an 'exit' transaction.
    * Balance is updated on last transaction of signed multisig.

## Network Technical Overview Document

* Payment Channel: Two party local consensus.
    * One on-chain transaction to set up a multisig.
    * Refund transaction signed by both parties (retuning coins) before channel open.
    * Either party broadcasts local 'state' to retrieve funds.
    * Channel state updated with no on-chain interaction.
* Updating local Tx State:
    * Prior states invalidated, if party broadcasts old state, other party can claim all coins.
    * Both parties have 'economic incentive'.
* Blockchain becomes a 'dispute resolution' backbone.
* "Time-locked transactions"
    * Each hop decrements the time-lock.
* Operates off-chain with on-chain enforcement.
    * Chain used if other party not cooperating.

### Methodology

1. Create ``Funding transaction`` (unsigned).
2. Create ``Commitment transaction`` (unsigned).
3. Sign Commitment Transaction.
4. Exchange commitment signatures.
5. Sign Funding Transaction.
6. Exchange funding signatures.
7. Post funding *on the chain*.


### Types of Transactions

* **Funding Transaction**: Both parties fund the input to the multi-sig.
    * Single 2-of-2 multi-sig.
    * Note: signatures not exchanged until spends created.
* **Commitment Transaction**: spend from the multi-sig.
    * Used to present the current balance.
* **Revokable Transaction**: spends from unique output, unique type of output script.
    * Only spend after a number of confirmations.
