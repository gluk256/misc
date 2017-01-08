This algorithm is suitable for those games, where outcome of every round for the player depends only on the RNG and (optionally) the number, chosen by the player, but not on the action of other players. E.g. it may be suitable for roulette, slots, etc., but not for those games, where outcome depends on the other players (as may be the case in lotteries, etc.). For example, a game of roulette could be modelled as a number of rounds where a single player plays against casino. In this case, we can generate a pseudorandom number, using the following  algorithm.

1. Casino generates the new private key (PrivKey) and publishes the corresponding public key (PubKey).
2. Casino creates a smart contruct, which contains the PubKey and the bounty. (Optionally: casino changes PubKey of the existing smart contruct, thus saving the transaction cost of creating a new contruct).
3. Player chooses the number (B) to bet on, and a random number (R). (He might even specify the range of numbers in certain format).
4. Player sends a transaction containing the ether bet, and the data: B and R.
5. The contruct concatenates the random number R with the public address (Addr) of the player's ether account (from which TX was sent). Thus, an intermediate value is stored in the contruct: V = R + Addr
6. Casino must sign the resulting value V with its PrivKey, thus producing the digital signature S = sign(PrivKey, V), and send the corresponding TX, containing S.
7. The contruct recovers the actual public key (APK) of the digital signature S, and verifies that it is equal to the pre-published PubKey (APK == PubKey). If APK does not match PubKey, or if casino fails to perform step 6 withing a predefined time frame, it tantamounts to cheating. In this case the bounty goes to the player along with the original bet, and the contruct is closed via suicide.
8. The contruct uses S as a seed for the predefined PRNG algorithm (e.g. SHA-3 based), which produces the lucky number (L) between 0 and 36.
9. If B corresponds L, the player wins, otherwise casino wins. The contruct sends the bet to the winner.
10. Now casino may close the contruct and recover the bounty, or initiate a new round of the game.

After casino has chosen the PrivKey, its actions become deterministic. The player can not predict the result of digital signature, and therefore his choice of the random number R can influence the outcome only in the same way as rolling the dice in the real life. Thus, neither of the participants can manipulate the outcome in any meaningful way.

The bounty that casino set on the contruct must be high enough to compensate the players for any possible opportunity loss. The time slot must be long enough to accomodate for any kind of network disruption. Although it gives the casino opportunity to postpone the outcome, it has no incentive to do so, since the outcome is still deterministic. Postponing without a valid reason will only result in reputational loss, but no financial gain is possible.

This contruct can be reused multiple times, provided that the player never chooses the same number R twice. This task could be assigned to the contruct -- it should reject any TX, where the same player uses the same number R in the same contruct twice.














