## Petrification Algorithm

Authors: 

Vlad Gluhovsky <gluk256@gmail.com>

Daniel A. Nagy <nagydani@epointsystem.org>

## Warning

Computer simulation has yilded negative results. 

This algorithm is probably useless.

## Introduction
The objective of the presented algorithm is to generate a fair random bit after *k* blocks in face of an adversary controlling 
0 < *m* ≪ 1 of overall hashing power. We assume that *m* < 1/2, because otherwise the blockchain project would be prone to 51%-attack.

We consider our algorithm to be fair, if for any predefined bias *ε*, it is possible to set a finite number of blocks *k*, so that the probability of certain outcome (0 or 1) is less than *1/2 + ε*.

## Algorithm Description
For each block, we define *Rᵢ* to be bit *i* from a pseudorandom IID bitstream *R* seeded by the block hash. 
The algorithm is initialized by setting *S* to *R₀* in the first block *b*. 
In each subsequent block *b+n* such that 1 ≤ *n* < *k*, *S* is flipped iff ∀(*i* ≤ *n*): *Rᵢ* = 1. 

In other words, *S* will be flipped in each subsequent block with exponentially diminishing probability.

## Calculations
Suppose, adversary is interested in the final outcome of S = 1.

Let us calculate the approximate probabilities of certain events.

Event C: next block is found by adversary.

*Pc* = *m*

Since *m* ≪ 1, and in order to simplify calculations, we assume that adversary can find only a single block. 

Event A: during the first block mining, adversary finds one or more blocks and finalizes one of them.

We assume that adversary will always finalize the block if the resulting value of S = 1. 

*Pa* = *Pc* / 2 = *m* / 2

Event H: honest participants find the first block and finilize the result.

We assume that honest participants always finalize the block they find.

*Ph* = 1 - *Pa* = 1 - *m*/2

Event 1: probability of S = 1 after the first block is finalized.

*P1* = *Pa* + *Ph* / 2 = *m*/2 + (1 - *m*/2) / 2 = 1/2 + *m*/4

Event J: during the mining the block number X, adversary finds the block.

*Pj* = *Pc* = *m*

Event K: during the mining the block number X, adversary finds the block AND all first X bits of the bitstream are 1.

*Pk* = *Pj* / 2^*X*

Let *z* = 1 / 2^*X*, then

*Pk* = *Pj* * *z* = *m* * *z*

Event B: same as event K, AND adversary finalize the block.

It happens if adversary was NOT satisfied with the pre-existing result (i.e. if S = 0).

Let *Prev* is probability of S = 1 in block number X-1. Then

*Pb* = *Pk* * (1 - *Prev*) = *m* * *z* * (1 - *Prev*)

Event M: during the mining the block number X, adversary finds the block AND NOT the all first X bits of the bitstream are 1.

*Pm* = *Pj* * (1 - *z*) = *m* * (1 - *z*)

Event N: same as event M, AND adversary finalize the block.

It happens if adversary was satisfied with the pre-existing result (i.e. if S = 1).

*Pn* = *Pm* * *Prev* = *Prev* * *m* * (1 - *z*)

Event Q: adversary finalize the block number X.

*Pq* = *Pb* + *Pn* = *m* * *z* * (1 - *Prev*) + *Prev* * *m* * (1 - *z*) = *m* * *z* + *Prev* * *m* - 2 * *Prev* * *m* * *z*

Event T: honest participants finalize the block number X.

*Pt* = 1 - *Pq*

Event Y: honest participants finalize the block number X, AND all first X bits of the bitstream are 1.

*Py* = *Pt* * *z* = (1 - *Pq*) * *z*

Event V: honest participants finalize the block number X, AND NOT all first X bits of the bitstream are 1.

*Pv* = *Pt* * (1 - *z*) = (1 - *Pq*) * (1 - *z*)

Event U: same as event Y AND S = 1.

It happends if previous value of S was 0.

*Pu* = *Py* * (1 - *Prev*) = (1 - *Pq*) * *z* * (1 - *Prev*)

Event W: same as event V AND S = 1.

*Pw* = *Pv* * *Prev* = (1 - *Pq*) * (1 - *z*) * *Prev*

Event I: honest participants finalize the block number X, AND S = 1.

*Pi* = *Pu* + *Pw* = (1 - *Pq*) * *z* * (1 - *Prev*) + (1 - *Pq*) * (1 - *z*) * *Prev*

Event X: S = 1 in block number X.

*Px* = *Pq* + *Pi* = *Pq* + (1 - *Pq*) * *z* * (1 - *Prev*) + (1 - *Pq*) * (1 - *z*) * *Prev*

