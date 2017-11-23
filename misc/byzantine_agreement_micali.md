# Byzantine Agreement made Simple - Silvio Micali

(https://www.youtube.com/watch?v=mqqEjzmQva8)[https://www.youtube.com/watch?v=mqqEjzmQva8]

## The Problem

* Need to make an agreement
* Some malicious members - lower numbers (bound 1/3 or 1/2)

## Communication Model

* Fixed number of players
* Synchronous network
    * Performs in rounds

## Adversarial Model

* Infinite power
* Corrupt any player
* Control + Coordinates all bad players
* Sees all messages.

## Old Goal

* Reach agreement between tolerating the fraction of bad players in constant round.
* If we do - has to be **probabilistic**.
    * FLP result says ``T+1`` rounds.
    * Ben-Or: O(sqrt(n)) bad players for O(1) rounds.
    * Rabin: n/4 bad players in O(1).
        * Common coin provided by external party.
* Micali: n/3 bad players in O(1) rounds.
    * Complex protocol!

## Today

* Approach:
    * Cryptographic BA
    * ``<n/3`` bad players
    * * 6 Trivial rounds
* Dolev and Strong (1983) - ``t < n/2`` in ``t+1`` rounds with signatures.
* Every player has a public key.
* There is a random string R independent of PubKeys(i).
* Utilises ``VRF``.
    * For each key, at most one Signature per message.
    * Model hash functions as a random oracle.
        * Hash of a signature is a unique random string for ``i`` and ``m``.

### Things proceed in rounds

* Quantity is ``Hash(Round, round number)``
* Instructions:
    1. Reaching agreement at the end of the round with probability ``>1/3``.
    2. Remaining agreement, if in agreement. (when you reach agreement, you stay there)
* Protocol:
    * Receive: 
        * ``b(i, r-1)`` from previous round [opinion of previous round].
        * Signature of the Quantity of the round from every "willing" playuer `j`.
            * At most N signatures.
    * If ``bit == 0 for > 2n/3`` then ``b(i, round) = 0``.
    * If ``bit == 1 for > 2n/3`` then ``b(i, round) = 1``.
    * Else,
        * ``rand_r = min_j H(SIG_j(Qr))``.
            * At most `n` 256-bit strings total.
            * Choose the minimum.
            * "random" 256-bit string.
        * ``b(i, r) = least_significant_bit(rand_r)``.
            * High probability that ``2/3`` have the same random bit.
    * Send: b(i, r)
* Example:
    * If an agreement on 0 exists, then agreement on 0 is kept.
        1. Receive ``b(j, r-1) == 0``
        2. Count number of 0s.
        3. Agreement on 0 is reached with probability ``2/3*1/2 = 1/3``.
            * Using the random coin
            * ``2/3`` honest are on 0.
            * ``1/2`` probability number is 0.

## How has this problem not been solved for 30 years?

* Authentication **does not equal** Digital Signatures.
    * Digital signatures are bits, can be hashed and manipulated.
* Computation **does not equal** Turing Machines.

## Furthermore

* M and Vainkuuntanathan: BA today - ``< n/2`` bad and same simplicity.
* ``Katz and Koo`` Feldman and M - ``< n/2``
* BA today - Propagation network as well.
* Player replaceability.
    * If you disseminate the network as well, players can be replaced.
