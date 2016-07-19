## Self-Witness Algorithm: Privacy Protection in a Fully Transparent System
By **Vlad Gluhovsky**

### Problem

Suppose a number of participants need to transfer arbitrary (but equal) amount of Ether from their original accounts (`src`) to their anonymous accounts (`dest`), e.g. for the purpose of anonymous voting. They need to make sure that the transfer in untraceble. Also any participant attempting to thwart the algorithm is punished and the others are rewarded. 

If their `dest` accounts already contain a small amount of Ether, they can use the following algorithm.

### Solution

1. Every participant generates a random private key and a corresponding public key, then encrypts his `dest` address with this public key.

2. Each `dest` account registers itself by transferring a small security deposit to the smart contract. Participation is closed after timeout has expired or if the number of participants has reached the limit.

3. All participants, whose `dest` accounts were successfully registered (transaction was validated), will transfer some predetermined Ether value (`sum`) from their source accounts (`src`) to the contract, attaching the encrypted `dest` address to each transaction for the possible arbitration.

4. As soon as the number of `src` participants will reach (or exceed) the number of `dest` participants, Ether transfer to `dest` is triggered. The contract will transfer all its Ether to the `dest` accounts, dividing it equally between them. The generated private keys should be deleted and never used again.

5. If the number of `src` is still less than the number of `dest` after timeout expires, arbitration is triggered. Every `src` participant must reveal his generated private key. Then the smart contract will decrypt all the data attached to transactions, and create a list of valid `dest` addresses. Any `dest` that is not in the list will lose its deposit. The rest of the Ether will be returned back to its original owners.

### Futher Considerations

Security deposit may be much less than the `sum`, but must be enough to pay for the possible arbitration, plus all the transaction costs, plus possible compensation to the honest participants. Therefore, the number of participants must be limited, and depend on the required security deposit.

Timeout should allow several attempts to send a transaction plus some additional time in case of network disruption, etc.

Another modification of the algorithm might stipulate the arbitration if the number of `src` will exceed the number of `dest`, in order to limit the risk, so that even in case of error any participant will never lose more than the predefined security deposit.
