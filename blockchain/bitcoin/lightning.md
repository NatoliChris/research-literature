# Understanding the Lightning Network (Dot points from articles)

## Problem with Bitcoin

* All nodes receive and store everything.
* Long wait times for simple transactions.
* Low scalability.
* Thought:
    * **Push Transactions Off Chain**
        * Not all nodes need to know transactions until final state.
        * Save the blockchain as a backbone.

## Payment Channels

* Private 'IOUs' being passed between 2 parties.
* As long as both aprties follow rules - all is well.
* Transactions occur (commitment) but not posted.
* User's are able to *cash out* at any time.
    * Both parties agree to commitments, if that commitment is posted then the money is withdrawn from the payment channel.

## How Payment Channels Work

* Scripting language allows conditions to spend coins.
* Alice and Bob create Tx with a 2-signature condition.
* New commitment transaction sending money back
    * Update the commitment transaction together, this is a symbol of 'sending' money.
* As long as commitment transaction not posted to blockchain, channel stays open.

## Invalidating Old Commitment Tx

* Problem: Trust others to destroy old commitments?
* Solution: Introduce a penalty if an old commitment is sent out.
* Bob/Alice must wait X blocks to withdraw money if they publish the commitment transaction.
* To invalidate commitment transaction:
    * Opposite party gives **Revokation Key** to other, indicating invalidation of old commitment.
        * Key *R* can withdraw ALL funds.
        * Encoded with the next commitment.

## Chaining Payment Channels 

* ``A <-> B <-> C``  - Alice is able to pay Carol through Bob and give reward to Bob for his services.
* Need to ensure Bob can't steal any money.
* ``Alice <-> Bob <-> Charlie <-> Daisy``: Alice pays Daisy but agrees to pay both Bob and Charlie for their services.
* ***Hash Time-Locked Contract*** (HTLC)
    1. Dasiy chooses value `P`.
    2. Daisy hashes `P` : ``H(P) = h_p``
    3. Dasie sends `h_p` to Charlie.
    4. Charlie sends to Bob, Bob sends to Alice.
    5. Alice creates a *HTLC* that won't pay Bob unless `P` is revealed to match `h_p`.
        * Only possible if Bob gets `P` from Charlie/Daisy.
    6. Bob signs HTLC with Charlie, Charlie signs with Daisy.
    7. Daisy receives funds, and sends `P` to Charlie, who can now unlock their fee reward.
        * Process repeated to Bob.
        * Funds are "bubbled" up.
* Enforced by Blockchain
    * New commitment tx output added to previous commitment tx.
        * Redeemable by Bob if P.
        * Redeemable by Alice after `x` days have passed and Bob has not produced `P`.
    * Same commitment transaction along the path, but each has shorter `x` period.
    * If Daisy disappears, Charlie can close the channel (commitment tx) without the HTLC output (Daisy never gets paid).
        * All other channels issue a new commit tx removing the HTLC outputs. (Unwinding)

## Effects of Lightning

* Fast:
    * Instant transaction with commitment.
    * Transaction in lightning use 'Time Locks'.
    * Expect customers to store infromation about high uptime, honest nodes.
* Lower fees:
    * Off chain transaction offers lower fees.
    * Competitive pricing.
* Privacy:
    * Only visible end-to-end.
    * Onion routing.
* Synchronous:
    * Requires interactions.
    * Hightened Security Risks:
        * Can't use cold-storage wallets.
        * If you hack a lightning intermediary, you can likely steal ALL the Bitcoin in it's payment channels.
* Could be a centralized structure:
    * Nodes becomes 'Hubs' that users pass through.
    * Simpler topology.
