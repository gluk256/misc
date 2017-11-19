## Self-Witness Algorithm: Privacy Protection in a Fully Transparent System
Author: **Vlad Gluhovsky**

### Problem

Suppose a number of participants need to transfer arbitrary (but equal) amount of Ether from their original accounts (`src`) to their anonymous accounts (`dest`), e.g. for the purpose of anonymous voting. They need to make sure that the transfer in untraceble. Also any participant attempting to thwart the algorithm is punished and the others are rewarded. 

If their `dest` accounts already contain a small amount of Ether, they can use the following algorithm.

### Solution

1. Each `dest` account registers itself by transferring a small security deposit to the smart contract. Participation is closed after timeout has expired or if the number of participants has reached the limit. Security deposit must be enough to cover all transaction costs.

2. All participants, whose `dest` accounts were successfully registered (transaction was validated), will transfer some predetermined Ether value (`sum`) from their source accounts (`src`) to the contract.

3. As soon as the number of `src` participants will reach (or exceed) the number of `dest` participants, Ether transfer to `dest` is triggered. The contract will transfer all its Ether to the `dest` accounts, dividing it equally between them.

4. If the number of `src` is still less than the number of `dest` after timeout expires, Ether transfer to `src` is triggered. The contract will transfer all its Ether back to the `src` accounts, dividing it equally between them.

### Futher Considerations

The `dest` participants must trust the `src` participants, but not vice versa. 

Security deposit may be much less than the `sum`, but must be enough to cover all the transaction costs, plus optional compensation to the honest participants. Therefore, the number of `dest` participants must be limited.

Timeout should allow multiple attempts to send a transaction plus some additional time in case of network disruption, etc.

Another modification of the Step 3 might limit the losses if the number of `src` will exceed the number of `dest`, so that any participant will never lose more than the predefined security deposit.

It is easy to extend this algorithm for the purposes of anonymous token transfer.


