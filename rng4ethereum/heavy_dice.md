## Heavy Dice

Authors: 

Vlad Gluhovsky <gluk256@gmail.com>

Daniel A. Nagy <nagydani@epointsystem.org>

## Introduction
The objective of the presented algorithm is to generate a pseudorandom number after *N* blocks in face of an adversary controlling 0 < *m* ≪ 1 of overall hashing power. More specifically, we assume the adversary to be able to prevent a specific block from being finalized in the blockchain with probability *m*. 

We consider our algorithm successful, if it would be prohibitively expensive for anyone to manipulate the resulting outcome. In other words, if it is possible to set parameters in such a way, that expectation of gain will be less than the cost of maintaining the bias.

## First Algorithm Description
We define *Rᵢ* to be bit *i* of the end result *R*. Also, for each block, we define *Hᵢ* to be bit *i* from a block hash *H*.

After all bets have been committed, we wait untill certain number of blocks is finalized in the blockchain. Then we look at the values of the *Hᵢ* in each of those blocks. If majority of *Hᵢ* contain the value of one, then *Rᵢ* shall be one; otherwise *Rᵢ* shall be zero. 

## Calculations
Suppose, our pseudorandom number R consists of a single bit, and adversary is interested in the final outcome of R = 1.

Let us calculate the approximate probabilities of certain events.

#### Event A
Next block is found by adversary.

*Pa* = *m*

Since *m* ≪ 1, and in order to simplify calculations, we assume that adversary can find only a single block. 

#### Event B
Next block is found by adversary AND is finalized.

We assume that adversary will finalize the block if the resulting value of R = 1. 

*Pb* = *Pa* / 2 = *m* / 2

#### Event C

Honest participants find the first block and finalize the result.

We assume that honest participants always finalize the block they if they find it.

*Pc* = 1 - *Pb* = 1 - *m*/2

#### Event D
Probability of R = 1 in a single block.

*Pd* = *Pb* + *Pc* / 2 = *m*/2 + (1 - *m*/2) / 2 = 1/2 + *m*/4

#### Event R
Probability of R = 1 after certain number (*N*) of blocks is finalized.

*Pr* = (*P1* + *P2* + *P3* + ... + *Pn*) / *N*

Since probability of R = 1 is equal in each block,

*Pr* = *Pd* = 1/2 + *m*/4

#### Event Q
Probability of R = 1 after certain number (*N*) of blocks is finalized if adversary was active only part time (during the mining of K blocks), and inactive during the mining of *N* - *K* blocks.

*Pq* = (*P1* + *P2* + *P3* + ... + *Pn*) / *N*

When adversary was active, the probability of R = 1 in a single block was *Pd*. Wnen not, 1/2.

*Pq* = (*Pd* * *K* + (*N* - *K*)/2) / *N* = 1/2 + (*m* * *K*) / (4 * *N*)

Let us calculate the bias:

*ε* = *Pq* - 1/2 = 0.25 * *m* * *K* / *N*

If adversary wants to maintain the initial value of bias (*m*/4), adversary must be active during the entire process of mining the *N* blocks. Thus, by increasing the number of blocks, we can proportionally increase the cost for adversary.

If the resulting random number contains *S* bits instead of one, then adversary will either try to manipulate only one bit (leaving all other bits random), or will try to manipulate the entire result. In the first case, the probability of certain outcome

*Ps* = *Pq* * 1/2^(*S*-1) = 1/2^*S* + (*m* * *K*) / (2 * 1/2^*S* * *N*)

Let *z* = 1/2^*S*. Then

*Ps* = *z* + 0.5 * *z* * *m* * *K* / *N*

In second case, it's more complicated. Let's recalculate all the events. 

#### Looking for specific number

*Pa* = *m*

*Pb* = *Pa* * *z* = *m* * *z*

*Pc* = 1 - *Pb* = 1 - *m* * *z*

*Pd* = *Pb* + *Pc* * *z* = *m* * *z* + (1 - *m* * *z*) * *z* = *z* + *m* * *z* - *m* * *z*^2

*Pr* = *Pd*

*Pq* = (*P1* + *P2* + *P3* + ... + *Pn*) / *N*

*Pq* = (*Pd* * *K* + (*N* - *K*) * *z*) / *N* = (*Pd* * *K* + *N* * *z* - *K* * *z*) / *N* = *z* + (*Pd* - *z*) * *K* / *N* 

*Pq* = *z* + (1 - *z*) *m* * *z* * *K* / *N*

*Ps* = *Pq*

So, in second case the probability of certain outcome is greater than in first case.

Assuming that adversary is active during the entire process of RNG (i.e. *K* = *N*), in second case

*Ps* = *z* + (1 - *z*) * *z* * *m* = *z* * (1 + (1 - *z*) * *m*)

If number of bits is big enough, then 1 - *z* ≈ 1.

*Ps* ≈ *z* * (1 + *m*)

Let us calculate the expectation of cost (*C*) for adversary. Suppose, block reward is *B* ether. Then during the mining process adversary have mined *N* * *m* blocks. Most of them were not finalized. Then 

*C* = *B* * *N* * *m* * (1 - *z*)

*C* ≈ *B* * *N* * *m*

Suppose, a single lot in this lottery costs *L* ether, the number of participants is *M*, and the winner's prise *W*. For simplicity assume *M* = 2^*S* = 1/*z*. Let us calculate the expectation of gain *G1* if adversary will buy a single lot.

*G1* = *Ps* * *W* - *L*

It makes sense to buy many lots in order to maximize the gain. 

Suppose adversary will buy *X* lots, where *X* = 1/*Ps*. Then

*G* = *G1* * *X* = (*Ps* * *W* - *L*) / *Ps*  = *W* - *L*/*Ps* ≈ *W* - *L* / (*z* * (1 + *m*))

Our goal is to make *C* greater than *G*.

*C* > *G* if

*B* * *N* * *m*  > *W* - *L* / (*z* * (1 + *m*))

*N* > (*W* - *L* / (*z* * (1 + *m*)) / (*B* * *m*)

*N* > (*W* * *z* - *L* / (1 + *m*)) / (*B* * *m* * *z*)

#### In case of "honest" lottery

*W* = *L* * *M* = *L*/*z*

*N* > (*L* - *L* / (1 + *m*)) / (*B* * *m* * *z*)

*N* > (*L* * *m* / (1 + *m*)) / (*B* * *m* * *z*)

*N* > *L* / (*B* * *z* * (1 + *m*))

If lottery is not "honest" (*W* < *L* * *M*), then *N* may be even less.

#### Example

Suppose, *m* = 0.1, *B* = 3 ether, *L* = 0.01 ether, *z* = 1/2^25 (more than 33 million lots). Then

*N* > 0.01 / (3 * 1/2^25 * (1 + 0.1))

*N* > 100000

At the rate of 12 seconds per block, it means approximately 14 days.
