### The Hashchain Algorithm

This algorithm is designed to provide deterministic pseudo-random numbers to be embedded in each Ethereum block. 

Every provider (Oracle) generates a random number, and then uses it as a seed to generate a very long hash-chain. This hash-chain should be stored securely on a server with a dedicated Etereum dapp running at all times. We assume, this hash-chain will be long enough to potentially provide data for 100 years (at the rate of approx. 1 million blocks per year he would need 100 million hashes of 32 bytes, i.e. ~3Gb of data). 

In a dedicated RNG smart contract a number of Oracles (the more the better) are registered before the execution starts, after which participation is closed. 

#### Participation

In order to participate, an Oracle must submit the last hash of his chain along with the initial bounty. Then, after the RNG contract starts running, each Oracle must submit the previous hash of his chain for every next Ethereum block. The contract validates the submitted value (that hash of this value is indeed equal to the previously submitted value). If validation fails or any Oracle fails to submit, this Oracle will lose his bounty and excluded from further participation. Also, this round of RNG is considered invalid. If everything is OK, then all those values from all the Oracles are XORed or otherwise used to generate a pseudo-random number according to the predefined algorithm. Thus, we have deterministic RNG. The outcome of each round is not predictable as long as at least one Oracle plays fair. Therefore the more Oracles participate, the better.

#### Incentives

In order to motivate the Oracles to maintain participation, each valid transaction should be rewarded by the owner of the smart contract (e.g. online casino, etc.). The owner should maintain the constant funding of this smart contract. Also, part of the fees earned by any Oracle might be stored in the contruct untill the Oracle gracefully terminates its participation. Otherwise (in case of cheating) those funds will be lost together with the bounty. 

Single Oracle is able to participate in multiple smart contracts, which would increase his motivation to play fair. We can predict that in case of success of this model, multiple smart contract will be maintained by different companies. In this case, the oldest Oracles will have advantage of higher reputation.

In order to prevent the information leaks, the contract might reward anyone who will publish the premature hashes. E.g. if Oracle X will publish the hash H in the block number Y, then anyone who will publish the hash which is due to the block number Y+2 will receive the entire bounty of Oracle X, and Oracle X will be kicked out.

#### Technical Challenges

Nobody can guarantee, that any particular transaction will be included in the next block. Also, we can expect a network disruption every now and then. Because of that a reasonable delay should be allowed, depending on the contract purpose. In case of online casino it could be up to a couple of days. Casino might intercept the valid transactions (or receive them via other channels), then calculate the pseudo-random number to be used in the next round of games, and act as though it is already embedded into the blockchain, since the hashes which correspond to particular block numbers are publish, and the corresponding transactions will be eventually included in the next blocks.

#### Disruption

If all the Oracles have published the valid hashes for the certain block, then we can say that pseudo-random number was generated honestly. But what if one of the Oracles refuse to participate in the next round? We should assume, that this round was manipulated, because its outcome was not deterministic.





















