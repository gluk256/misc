### The Ukrainian Algorithm

This algorithm is suitable to run an Ethereum-based lottery.

1. The organizer generates a random number X, and calculates its SHA-3 hash: Y = hash(X). Also, he produces a certain nonce (N), e.g. a timestamp of the current block.
2. The organizer creates a smart contract, which contains the hash (Y), nonce N, optional limits in number of participants, and the Ether bounty.
3. Every prospective participant generates a random key for symmetric encryption (K), and encrypts the nonce N using the predefined algorithm: E = encrypt(K, N), where E is the result of encryption of N with the key K. Then calculates its SHA-3 hash of E: H = hash(E). This predefined algorithm should be, for example, modified DES with key size being equal to the block size. The size of nonce should be also equal to the block size.
4. During the certain (big) time slot, participants send transactions (TX), containing the hash (H). This step is non-binding, no Ether bounty is required. After timeout expires, any further participation is closed. The smart contract rejects those TX, where H does not satisfy the size requirements. 
5. During the next (smaller) time slot, participants send TX with the corresponding E values, and the predefined Ether value (bets), which corresponds to the price of lottery ticket. The bet should be enough to cover the costs of the Arbitration.
6. The smart contract validates those TX, and rejects the invalid ones (wrong Ether value or wrong E, which does not correspond to the previously published H).
7. After timeout expires, any further (valid) TX are rejected. At this point the outcome of the game becomes deterministic.
8. During the next (very big) time slot, participants send TX with their keys K. The smart contract verifies the validity: E == encrypt(K, N). Invalid TX are rejected.
9. For any participant, who fails to produce the valid TX before the timeout expires, Arbitration is triggered. Also, his bet is confiscated. Since the game is now deterministic, no time limit is necessary for the next steps, except 13. Everyone will be allowed to submit a valid TX, including the step 13.
10. Arbitration is breaking the DES algorithm by the brute force attack. After this step all the keys K will be revealed, without exception. This step is responsibility of the organizer, but everyone is allowed to submit the valid TX containing the corresponding value.
11. Now the smart contract calculates the "sum" (S) of all the keys K by XORing them. The value of S is stored in the contract.
12. The organizer sends TX with his random number X. If it does not correspond to the previously published hash Y, TX is rejected.
13. If the organizer fails to produce the valid TX until timeout expires, he loses the bounty, which is divided among the participants. The smart contract also sends all the bets back to the participants. Then the contract will be closed.
14. If everything is OK, the smart contract performs the XOR operation on S and X, thus producing the pseudorandom seed: SEED = S ^ X.
15. After the SEED is revealed, the final round of calculation starts, with the purpose of finding the final number Z. Anyone is allowed to submit the number Z via TX to the contract. There is no time limit for this step.
16. After the number Z is submitted and validated, Z is used by the contract as the seed for the predefined PRNG algorithm to define the winner (winners).

#### Discussing the Arbitration

DES is a very old algorithm. It was used for many years, and was profoundly analyzed by the cryptographic community. It was  broken by the brute force attack in 1997. In 2008 SciEngines GmbH demonstrated breaking DES in less than one day, using 128 Spartan-3 machines. Now it is possible to break it even faster. But it is still quite expensive and time-consuming task.

But what if somebody wants to disrupt the game by submitting just a random number instead of proper E (encrypted by DES with certain password)? Well, for the purposes of Arbitration, it does not matter at all. All DES does is just a permutation within a certain field of values, and therefore, if the key size is equal to the block size, for any value N and encrypted block E there is one (and only one) key K such, that E = encrypt(K, N). Finding this K is the purpose of the brute-force attack, which guarantees success in this case, even if E was just a random number.

#### Discussing the Step 15

After the seed is revealed, the new round of calculations starts. The organizer (or whoever else is willing to do so) needs to consequently bruteforce a certain (big) number of Simplified DES (or similar encryptions). This should be quite expensive and time-consuming task. However, validity check is cheap and quick, and everyone can perform it easily (this task will be assigned to the smart contract). Parameters of this task should be such, that it should not be feasible to break it on a single computer on the fly (as required by the possible attacker -- within the single block mining time). Besides, the number of iterations should be such, that even outsourcing this task should not be feasible because of the latency. But the organizer should be able to perform it in a matter of days.

It is also possible to implement Step 15 using the "Witness vs. Challenger" algorithm.

#### Possible Attack

How can anyone manipulate the outcome of the game in any meaningful way? Theoretically, during the step 5, but before one final TX is submitted, one can brute-force all the DES-encrypted values E, then calculate the outcome of the game, and if this outcome does not satisfy him, abstain from sending the final TX. This attacker must also know the organizer's secret number X, so we assume, if could only be the organizer. But it would be very expensive. Besides, step 15 is supposed to ensure that even after all the DES encryptions are broken (at the enormous cost), another time-consuming operation needs to be done during the time of mining of one Ethereum block, which is supposed to be impossible. And finally, what does he gain from this venture? Suppose, he can prevent one particular outcome of the game. But he can not define the next outcome, since he can not produce new keys K, only withdraw the existing ones. If he participates in the game, then the gain is equal to the price of tickets he holds. If we know the price of breaking DES, then we can define the price of ticket to be such, at which no economic gain is impossible under any circumstances.

#### Further Discussion

In order to make the outcome even less predictable, the organizers might offer a large reward to a miner, who will submit the last transaction during Step 5. It will increase probability of at least one transaction being submitted in the last block. Most miners will hold a valid transaction till the very last moment, and only one of them will be able to submit it.

Because of dynamic nature of this algorithm, the first lottery organizer who implements it will enjoy the world-wide interest and publicity in all kind of software-relate media. The amount of attention will be unprecedented, and therefore will hugely add to the marketing of his project.

Also, some people might trust the lottery design, and therfore might be interested only in buying the ticket without participating in RNG. The organizers might provide an option to buy a ticket in a single transaction for such non-contributing participants.
