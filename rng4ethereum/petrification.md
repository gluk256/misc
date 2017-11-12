## Petrification Algorithm

Authors: 

Vlad Gluhovsky <gluk256@gmail.com>

Daniel A. Nagy <nagydani@epointsystem.org>

## Introduction
The objective of the presented algorithm is to generate a fair random bit after *k* blocks in face of an adversary controlling 
0 < *m* ≪ 1 of overall hashing power. We assume that *m* < 1/2, because otherwise the blockchain project would be prone to 51%-attack.

We consider our algorithm to be fair, if for any predefined bias *ε*, it is possible to set a finite number of blocks *k*, so that the probability of certain outcome (0 or 1) is less than *1/2 + ε*.

## Algorithm Description
For each block, we define *Rᵢ* to be bit *i* from a pseudorandom IID bitstream *R* seeded by the block hash. 
The algorithm is initialized by setting *S* to *R₀* in the first block *b*. 
In each subsequent block *b+n* such that 1 ≤ *n* < *k*, *S* is overwritten by *Rₙ₊₁* iff ∀(*i* ≤ *n*): *Rᵢ* = 1. 

In other words, *S* will be overwritten by a random bit in each subsequent block with exponentially diminishing probability.

## Calculations
Suppose, adversary is interested in the final outcome of S = 1.

Let us calculate the approximate probabilities of certain events.

Event D: next block is found by adversary.

*Pd* = *m*

Event E: if during the mining process adversary does not finilize blocks, then probability of adversary finding N consecutive blocks before next block is finilized by the honest participants:

*Pe* = *m*^*N*

Event C: if during the mining process adversary does not finilize blocks, then probability of adversary finding 1 or 2 or .... or N consecutive blocks before next block is finilized by the honest participants:

*Pc* = *m*^1 + *m*^2 + ..... + *m*^*N*

Let us calculate *Pc* for all possible outcomes (N -> ∞).

*Pc* = *m*^1 + *m*^2 + ..... + *m*^*N* + ...... + *m*^∞

In case *m* ≪ 1, *Pc* ≈ *m*. In case *m* -> 1/2, *Pc* ≈ 2*m*. 

To simplify the further calculations, we assume *Pc* = *m*.

Event A1: during the first block mining, adversary finds one or more blocks and finalizes one of them.

We assume that adversary will always finilize the block if the resulting value of S = 1. 

*Pa1* = *Pc* / 2

Event H1: honest participants find the first block and finilize the result.

We assume that honest participants always finalize the found block.

*Ph1* = 1 - *Pa*

Event 1: probability of S = 1 after the first block is finalized.

*P1* = *Pa1* + *Ph1* / 2

Event J: during the mining the block number X, adversary finds the block.

*Pj* = *Pd* = *m*

Event K: during the mining the block number X, adversary finds the block AND all first X bits of the bitstream are 1.

*Pk* = *Pj* / 2^*X*

Let *z* = 1 / 2^*X*, then

*Pk* = *Pj* * *z* = *m* * *z*

Event B: same as event K, AND adversary finalize the block.

It happens if adversary was NOT satisfied with the pre-existing result (i.e. if S = 0), AND new result is S = 1.

Let *Prev* is probability of S = 1 in block number X-1. Then

*Pb* = *Pk* * (1 - *Prev*) * *z*/2 = *m* * *z*^2 * (1 - *Prev*) * 1/2

Event M: during the mining the block number X, adversary finds the block AND NOT the all first X bits of the bitstream are 1.

*Pm* = *Pj* * (1 - *z*) = *m* * (1 - *z*)

Event N: same as event M, AND adversary finalize the block.

It happens if adversary was satisfied with the pre-existing result (i.e. if S = 1).

*Pn* = *Pm* * *Prev* = *Prev* * *m* * (1 - *z*)

Event Q: adversary finalize the block number X.

*Pq* = *Pb* + *Pn* = *m* * *z*^2 * (1 - *Prev*) * 1/2 + *Prev* * *m* * (1 - *z*)

Event T: honest participants finalize the block number X.

*Pt* = 1 - *Pq*

Event S: honest participants finalize the block number X, AND S = 1.

*Ps* = *Pt* * (*z*/2 + (1 - *z*) * *Prev*) = (1 - *Pq*) * (*z*/2 + (1 - *z*) * *Prev*)

Event X: S = 1 in block number X.

*Px* = *Pq* + *Ps* = *Pq* + (1 - *Pq*) * (*z*/2 + (1 - *z*) * *Prev*)

