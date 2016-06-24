## Further Analysis

### Economics of Cross-Witness

If any participant cheats, his money at stake must be enough to cover for all the expenses. The contract denomination must be greater than aggregated transaction costs of all the participants, plus potential arbitration costs. The aggregated costs are proportionate to the number of participants, and therefore there must be a limit to the number of participants for each denomination of smart contract. If the number of people exceeds the contract limit, it might result in shortages/queues. This gives the malicious entities an opportunity to launch DDoS attack. One of the possible solutions: multiple contracts + one "router" contract, through which all the TX must be initiated. RANDAO algorithm should be used to assure, that router contract is fair ("nothing up my sleeve"), and besides, may not be manipulated by miners or anyone else.

### Onion Concept

We assume, that multiple iterations will be necessary to achieve the desired probability of untracebility. If we could do several iterations at once, we might save on transaction costs, execution time, and -- which might be even more important -- mental costs of initiating transaction, choosing the available contract in case of queues, and managing the multiple accounts (although this could be automated, but still requires external overhead).

Here is one of the possible solutions. During the Witness Selection step, everybody should select certain (predefined) number of witnesses, then repeatedly encrypt his data with their public keys, then publish the resulting encrypted data ("onion"). Next step remains the same, except that it must be repeated as many times, as required to "unwind the onion".

### Onion Economics

If the original version of Cross-Witness algorithm runs smoothly, it requires the following transactions: 
- initial money transfer with attached public key
- publication of encrypted `dest`
- publication of decrypted `dest`
- signing and dissolution

Now, if we add one extra iteration (in case of two-layer onion), we only add one more TX -- publication of decrypted onion layer. If we add 4 iterations (five-layer onion), the TX costs will only double, but probability of failure will be p^5. However, arbitration costs increase dramatically. So, the economic calculations should be corrected correspondingly. It might also mean, that onion extension will be economically viable only for large denomination contracts.

## Without Witnesses

If `dest` already contains some Ether, then we don't need the witnesses at all. The algorithm becomes extremely simple:

1. Each participant `p` transfers some predetermined Ether value (`stake`) from their source accounts (`src`) to the contract, attaching the chosen public key `public[p]` and encrypted address of his anonymous account (`dest`) for the possible Intermediate Arbitration. 

2. Every `dest` account registers itself, transferring security deposit to the contract. The number of transactions in this step is not limited -- anyone who is willing to risk his deposit is allowed to participate. After this step is complete, each participant `p` must sign the contract from his source account.

3. If the number of `dest` accounts is equal (or less) than the number of initial participants, and everyone has signed the contract, than `stake` will be transferred to the `dest` accounts.

4. If somebody has not signed, or the number of `dest` accounts is greater than the number of `src`, then arbitration is triggered. Everybody must reveal his private key associated with his `src`, then the contract will automatically decrypt the designated `dest` addresses, and `dest` addresses that were not pre-registered will lose their deposit. Non-signing without valid reason tantamounts to cheating.

## Perfect Anonymity

Creating anonymous account with sufficient probability of privacy is an onerous task. But once it is created, it could be further used for an easy one-step anonymization of multiple accounts. Those accounts will then enjoy the perfect anonymity.

The Witness Selection step of the original Cross-Witness algorithm is the weakest link -- one must rely on the chosen witness to protect his privacy. But if we use anonymous accounts for publishing encrypted destinations, the whole algorithm becomes trustless. Let's redesign the Cross-Witness algorithm.

### Registration

Each participant `p` transfers some predetermined Ether value `stake` from their source accounts to the contract, attaching the chosen public key `public[p]` and encrypted address of his anonymous account (`ctrl`) for the possible Intermediate Arbitration.

### Control Transfer

Each participant generates a pair of keys to be associated with his anonymous account ("Controller" or `ctrl`). Then performs the Registration step again, this time using his `ctrl`. Another security deposit must be attached to this transaction. The number of transactions is not limited -- anyone who is willing to risk his deposit is allowed to participate. After this step is complete, each participant must sign the contract from his source account. If the number of Controllers is equal (or less) than the number of initial participants, and everyone has signed the contract, than the control over the contract execution has been successfully transferred to the anonymous Controller accounts, which will perform all the subsequent steps in lieu of source account. The second security deposit must be immediately returned to the Controllers.

### Intermediate Arbitration

If somebody has not signed, or the number of Controllers is greater than the number of sources, Intermediate Arbitration is triggered. Everybody must reveal his private key associated with his `src`, then the contract will automatically decrypt the designated `ctrl` addresses, and Controllers that were not pre-registered, lose their deposit. Non-signing without valid reason tantamounts to cheating.

### Usage

Once Control Transfer has been conducted and security deposits have been returned, it is possible to transfer unlimited amount of Ether from `src` to `ctrl`, without even bothering with `dest`. The transfer becomes trustless and riskless. Why using the `dest` at all? Well, `ctrl` can be used for Ether transfer only if it already contains some Ether (enough for security deposit). But if you need to transfer Ether to an empty anonymous account, then you need to use the `dest`. Also, once sufficient funds have been transferred to `ctrl`, there might be a need to split one anonymous account into multiple independent accounts (to be used further for multiple voting sessions). It is trivial to change this algorithm, so that one `src` and one `ctrl` will produce multiple `dest` accounts in one cycle. The resulting anonymous accounts might be in turn pumped with funds, then split again, and so on.