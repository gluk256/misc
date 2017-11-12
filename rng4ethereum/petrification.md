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
Suppose, adversary is interested in the final outcome of 1.

Let us calculate the approximate probabilities of certain events.

Event A: first block is found by the honest participants (not the adversary). 

*Pa* = 1 - *m*

Event B: first block is found by the honest participants AND finalized.

We assume that honest participants always finalize their blocks. Same as in Event 1.

*Pb* = 1 - *m*

Event C: first block is found by the honest participants AND finalized AND S = 1.

*Pc* = (1 - *m*) / 2

Event D: first block is found by adversary.

*Pd* = *m*

Event E: one or more blocks are found by adversary AND finalized with S = 1.

We assume that adversary always discards blocks if S = 0 and then continues mining in hope to find another block.

*Pe* ≈ (*m* + *m^2* + *m^3* + *m^4* + ..................[ad infinitum]) / 2

Event 1: first block is finalized with S = 1.

*P1* = *Pc* + *Pe* ≈ (1 + *m^2* + *m^3* + *m^4* + ..................[ad infinitum]) / 2

In case *m* ≪ 1, *P1* ≈ (1 + *m^2*) / 2.

In the worst possible case *m* = 1/2, *P1* = (1 + 2*m^2*) / 2.

To simplify further calculations, we assume *m* ≪ 1 and 

*m^2* + *m^3* + *m^4* + .......... ≈ *m^2*. 

In other words, we assume that adversary either does not find a block or finds only one block.

Event F: second block is found by the honest participants AND first two bits are 1.

*Pf* = (1/2^2) * (1 - *m*)

Event G: second block is found by the honest participants AND first two bits are 1 AND S = 1.

*Pg* = *Pf* / 2 = (1/2^2) * (1 - *m*) * 1/2

Event H: second block is found by the honest participants AND not all of first two bits are 1.

*Ph* = (1 - 1/2^2) * (1 - *m*)

Event J: second block is found by the honest participants AND not all of first two bits are 1 AND S = 1.

*Pj* = *P1* * *Ph* = *P1* * (1 - 1/2^2) * (1 - *m*)

Event K: second block is found by the honest participants AND S = 1.

*Pk* = *Pg* + *Pj* = (*P1* * (1 - 1/2^2) * (1 - *m*)) + ((1/2^2) * (1 - *m*) * 1/2) = (1 - *m*) * (*P1* - *P1* * 1/2^2 + 1/2 * 1/2^2)

Event M: second block is found by adversary AND first two bits are 1.

*Pm* = 1/2^2 * *m*

Event N: second block is found by adversary AND first two bits are 1 AND second block is finalized by adversary.

Adversary will finalize their block iff the preexisting value of S is 0 AND new value of S is 1.

*Pn* = *Pm* * (1 - *P1*) * 1/2 = 1/2^2 * *m* * (1 - *P1*) * 1/2

Event NN: second block is found by adversary AND first two bits are 1 AND second block is NOT finalized by adversary.

*Pnn* = *Pm* * (1 - (1 - *P1*) * 1/2) = *Pm* * (1 + *P1*) * 1/2 = 1/2^2 * *m* * (1 + *P1*) * 1/2

Event Q: second block is found by adversary AND not all of first two bits are 1.

*Pq* = 1/2^2 * *m*

Event S: second block is found by adversary AND not all of first two bits are 1 AND second block is finalized by adversary.

Adversary will finalize their block iff the preexisting value of S is 1.

*Ps* = *Pq* * *P1*

Event SS: second block is found by adversary AND not all of first two bits are 1 AND second block is NOT finalized by adversary.

*Pss* = *Pq* * (1 - *P1*) = 1/2^2 * *m* * (1 - *P1*)

Event T: second block is found by adversary AND second block is finalized by adversary.

Adversary will finalize their block if the resulting value of S is 1.

*Pt* = *Pn* + *Ps* = 1/2^2 * *m* * (1 - *P1*) * 1/2 + (1/2^2 * *m* * *P1*)

Event U: second block is found by adversary AND rejected by adversary.

*Pu* = *Pnn* + *Pss* = (1/2^2 * *m* * (1 + *P1*) * 1/2) + (1/2^2 * *m* * (1 - *P1*)) = 1/2^2 * *m* * (3/2 - *P1*/2)

Event V: second block is found by adversary AND rejected by adversary AND S = 1.

If second block is rejected by adversary, then it will be finalized by honest participants.

*Pv* = *Pu* * *Pk* 

*Pv* = 1/2^2 * *m* * (3/2 - *P1*/2) * *P1* * (1 - 1/2^2) * (1 - *m*)

Event 2: S = 1 after second block is finalized.

*P2* = *Pk* + *Pt* + *Pv* 

*P2* = (1 - *m*) * (*P1* - *P1* * 1/2^2 + 1/2 * 1/2^2) + 1/2^2 * *m* * (1 - *P1*) * 1/2 + (1/2^2 * *m* * *P1*) + 1/2^2 * *m* * (3/2 - *P1*/2) * *P1* * (1 - 1/2^2) * (1 - *m*)

Event X: S = 1 after block number X is finalized.

Let *Pp* is probability of S = 1 in the previous block (number X-1), and *z* = 1/2^X. 

*Px* = (1 - *m*) * (*Pp* - *Pp* * *z* + *z*/2) + *z* * *m*/2 * (1 - *Pp*) + *z* * *m* * *Pp* + *z* * *m* * (3/2 - *Pp*/2) * *Pp* * (1 - *z*) * (1 - *m*)

