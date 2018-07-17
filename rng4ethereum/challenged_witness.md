### RNG Algorithm "Challenged Witness"

#### by Vlad Gluhovsky & Daniel Nagy

This algorithm is designed to generate deterministic pseudo-random numbers from a block hash or any other seed revealed in certain Ethereum block in such a way, that the outcome will be unpredictable at the time of mining. It is suitable for slow games like lottery, but not for roulette. 

After a predefined block is mined, one or more participants obtain the seed (through the some algorithm) and create a hash-chain of a certain length. It should be done off-chain, using either ASIC or general purpose computer. The hashing algorithm should be the same as used for cryptocurrency mining (proof of work): SHA-2, SHA-3, etc. We assume, that the industry has already optimized the hardware for that task, since there was a huge incentive to do so. Mining process has been profoundly analysed by many people, and we can reasonably assume that the best mining equipment offered on the market today can not be rapidly improved. Due to inherently sequential character of the hash-chain, using multiple devices in parallel would not accelerate the task much. Therefore, we can approximately assess the time required to produce the hash-chain using the best mining equipment up to date. If producing the required hash-chain will take several times longer than average block mining time, we can be sure that at the time of mining it will be impossible to predict the outcome, and therfore the miners will be unable to bias or manipulate it. Once the block is mined and embedded into the blockchain, the outcome becomes deterministic.

For example, if the best ASIC up the date will produce 50 MHash/sec, then computing 50 billion links of hash-chain will take appoximately 1000 seconds, which will make it impossible to predict the outcome during the mining of the particular block. In order to make fraud even more difficult, we can offer additional reward to the miner of that block, thus increasing the miners' competition and reducing this block's mining time. Additionally, rather than using the block hash as a seed, we can obtain the seed in a more complicated way (e.g. some commit/reveal algorithm).

After the hash-chain is computed, the last hash of the hash-chain will be published in a dedicated smart contract by one or more participants (witnesses). This contract should offer a reward to incentivize the honest participants. 

To simplify the further discussion, we will consider only a single witness case. 

The smart contract will be unable to verify the outcome automatically, due to the gas limit in block, prohibitive cost, and other restrictions of EVM.  In order to verify the published hash we will use the Arbitration algorithm.

## Arbitration

After the witness publishes the outcome of hash-chain computation in a dedicated smart contract (along with attached bounty), during certain period of time people will be able to verify the outcome externally. If anyone detects fraud, one or more independent verifiers will be able to challenge the witness via a transaction to this smart contract before timeout expires. Any such challenge will also require a bounty (in order to discourage frodulent challenges resulting in postponed outcome). Please note, that any challenge can only postpone, but not change the outcome, which is inherently deterministic at this point. If the challenged witness will fail to defend himself, he will lose the bounty. Otherwise, the challenger will lose. 

The arbitration algorithm iterates through the following steps:

0. Set the first (F) and the last (L) link of the hash-chain for next iteration. F=0, L=N (where N is the length of the entire hash-chain).
1. The witness must publish the hash corresponding to the link number (F+L)/2 of the hash-chain. If he fails to do withing the predefined time frame, he loses.
2. The challenger must indicate if this hash is correct or not. If he fails to do within the predefined time frame, he loses.
3. Divide the hash-chain. In case of yes (correct): F=(F+L)/2; L is unchanged. Otherwise: L=(F+L)/2; F is unchanged.
4. If F-L > P (where P is predefined number), go to step 1. 
5. If F-L <= P, the smart contract will be able to identify the winner automatically, by calculating the corresponding small hash-chain.

## Further Discussion

Anyone should be allowed to participate as witness, or challenger, or both. The number of witnesses in a single smart contract may be unlimited. The number of challengers to the single witness may also be unlimited. However, the response time for the witness should be increased if multiple participants will challeng him. The required bounty should be such as to prevent the unlimited delay -- the frodulent participants should eventually run out of money. The confiscated bounties should be stored in the smart contract untill the end of the entire game, at which point they should be shared between the honest participants. 

The result published by a witness will be accepted by the smart contract as correct, if during certain period of time this witness will be able to defend himself against all the challengers, or remain unchallenged. The smart contract should be open for participation untill at least one witness wins.

When choosing number P, we must be careful. The gas required for step 5 must not exceed the current gas limit in a single block, otherwise this step will be impossible to execute. Alternatively, P should be adjusted dynamically, according to the current gas limit.

## Conclusion

Since everyone is allowed to participate as a witness, the correct outcome will be guaranteed even if only one participant is honest and everybody else is cheating.
