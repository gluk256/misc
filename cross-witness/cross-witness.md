# The Cross-Witness Algorithm: Privacy Protection in a Fully Transparent System
By **Vlad Gluhovsky** and **Gavin Wood**

# Introduction

Being based upon a quasi-Turing-complete (quasi because it's actually bounded) virtual machine, Ethereum is an extremely versatile system. However one of its greatest strengths---universal auditability---seems to lead to a fatal flaw, namely an inescapable lack of privacy. Here we demonstrate an algorithm in order to prove that this is not the case.

The algorithm could be used to make an Ethereum contract which, given two sets of addresses sources, `src`, and destinations, `dest`, will guarantee exactly one of two possible eventualities:

- For each address in `src`, the controller of that address controls a corresponding address in `dest` (though the two cannot be related *a priori*).

or 

- Guarantee each participant attempting to thwart the algorithm is punished and the others are rewarded.

Because the credible threat of punishment, we expect very few attempts to cheat and thus normal operation will lead to the first guarantee only.

## Description

### Formal Terms

We consider the system to have a set of participants, each with two addresses: `src` and `dest`. We consider each participant to control an address in each, and allow subscription in order to identify corresponding addresses:

```
FOREACH p IN src :
   THERE-EXISTS-A-UNIQUE dest[p] :
      WHERE dest[p] IS-A-MEMBER-OF dest
```

### Algorithm

Every participant `p` generates a random pair of cryptographic keys `public[p]` and `secret[p]`. The secret component is kept hidden from all other participants.

#### Registration

All participants must register their interest in taking part; this requires them to stake a security deposit. This will be lost in the case of malicious behaviour, but would be returned in the case of honest behaviour.

Like all other phases, this phase has a finite window of time in which it is valid. After the timeout expires, no other participants may join. This is the only phase where the timeout is strictly necessary (assuming an otherwise unlimited number of participants); in all other phases the timeout is merely a last resort to avoid a denial-of-service attack: assuming each participant takes action promptly, such timeouts are unneeded.

We assert that each participant `p` has transferred some predetermined Ether value `stake` from their source accounts to the contract, attaching the chosen public key `public[p]`.

```
FOREACH p IN src :
   PUBLISH :
      WITH-IDENTITY p
      WITH-VALUE stake
      public[p] := PUBLIC_COUNTERPART_OF secret[p]
```

There are a number of minor variants of the basic algorithm: It might (or might not) restrict the number of participants; some use-cases would require a fixed, maximum or minimum number of participants. Furthermore, it may be that there is a predetermined set of valid participants (e.g. the case with voting systems). It is assumed that this logic is contained in this registration phase.

#### Witness Selection

Every participant must choose a witness for themselves. For maximal protection, this should be done randomly, but other strategies may be employed; these are discussed later. The participant should then take the witness's public key, encrypt his destination address with it, and send as additional data to the contract.

```
FOREACH p IN src :
   PUBLISH WITH-IDENTITY p :
      statement[p] := dest[p] ENCRYPTED-WITH public[witness]
   WHERE witness := RANDOM-MEMBER-OF part
```

If a participant fails to publish an encrypted destination within certain time, then:

- they will lose their stake, which will be divided between the other participants;
- they will be excluded from further participation;
- this step will be repeated again, as many times as necessary.

At this point, all the destination addresses have been encrypted with one of the known public keys, and published. Only the owner of the corresponding key knows to whom the encrypted destination address belongs. Some keys might have been used multiple times whereas others never used at all.

Importantly, all participants `p` are certain that their choice of witness `witness` is potentially disinterested. There is no way to impose a particular, malign, witness upon any participant. Some participants might even choose themselves as witnesses and encrypt their destinations with their own private keys. Participants may even publish some garbage data instead of legitimately encrypted address---we will deal with this particular problem within the framework of *Arbitration*, later.

#### Validation

All participants must go through all published encrypted data (without exception), and try to decrypt every entry with their own private key. In every case of success, they must publish the corresponding (decrypted) destination address as additional data to the the contract. Before the time-out expires, everyone is allowed to publish an arbitrary number of addresses, not exceeding the total number of participants.

```
FOREACH p IN src :
   FOREACH s IN statement :
      IF CAN-DECRYPT s WITH secret[p] THEN :
         LET partner := s DECRYPTED-WITH secret[p]
         PUBLISH testaments[partner] WITH-IDENTITY p
```

Now, the list of destination addresses is published in plain format. All participants and onlookers can see who has published the testimony of any particular address, though only the publishing witness knows to whom it actually belongs. If the number of destination addresses is equal to the number of source addresses, then we are in a potentially good state. Otherwise, the *Arbitration* process will be triggered.

#### Confirmation
All participants `p` who can find their destination `dest[p]` in the list, must issue an acknowledgement to all others before timeout expires that they believe the apparent list of destinations is complete. If the destination is absent (generally through a malperforming or malign witness), the participant must submit a complaint against their witness---this will trigger *Arbitration*. Failure to act within the timeout is tantamount to cheating---it will also trigger *Arbitration*. 

```
FOREACH p IN src :
   IF p IS-MEMBER-OF testaments THEN :
      PUBLISH confirms[p] WITH-IDENTITY p
```

> **NOTE**
> The formal complaint is not strictly necessary for *Arbitration* to run. We use it only to provide a means for short-cutting to the *Arbitration* process, thus reducing time cost.

If all participants sign the contract, then the initial stake will be returned to each corresponding destination and the system will mature into a state proclaiming the first, key, guarantee; namely that it is possible to make a 1:1 mapping between `src` and `dest` such that the mapped items share the same controller:

```
IF confirms == src THEN :
   FOREACH d IN dest :
      BESTOW stake ON d
   ASSERT :
      FOREACH p IN src :
         THERE-EXISTS-A-UNIQUE d IN dest :
            CONTROLLER(p) == CONTROLLER(d)
ELSE :
   ARBITRATION
```

### Arbitration

The goal of Arbitration is to identify the cheaters. There are four possible cases:

1. *Failure to act after timeout expires in the Confirmation stage:*
 The cheaters are already identified.

2. *Formal complaint has been submitted by a participant (*`plaintiff`*) against one of the participants (*`accused`*):*
 The accused witness must publish the secret counterpart of their published public key (failure to do so, or publication of an invalid counterpart defaults to being identified as `malign`). Participants may then verify that the plaintiff had indeed correctly encrypted their destination with the accused's public key, `public[accused]`. If they had, then the accused is identified as `malign` for not publishing the testimony for the plaintiff. If they had not, then the plaintiff is identified as `malign`.
 
3. *The number of published destinations is less then the number of source addresses:*
 All of the participants are still obliged to act (either sign the contract or complain). Every complaint will be processed as in previous case. If no one complains and everybody signs, we proceed as in the case of success, except that at least one participant will lose their stake, which will be shared between the other, honest, participants.

4. *The number of published destinations is greater then the number of source addresses:*
 In this case everybody is under suspicion. If nobody complains, than everybody must prove themselves innocent, as in case (b). At least one participant will be identified as `malign`.

#### Dissolution
Once a number of `malign` participants are identified, they lose their Ether, which will be shared among the honest participants to cover the transaction and opportunity costs. The agreement will then be dissolved, and the total Ether will be transferred back, split equally among the source accounts of honest participants. The destination accounts must then be disguarded since their anonymity is compromised.

```
LET honest := src BUT-NOT-IN malign
LET payout := MEMBER-COUNT(src) / MEMBER-COUNT(honest) * stake
FOREACH p IN honest
   BESTOW payout ON dest[p]
```

### Aftermath

In the case of failure, we have gained compensation Ether for our trouble; we repeat until we have a success.

In case of a success, we know that our chosen witness was potentially malicious, and as such that the anonymity of our destination address was possibly compromised. Let's conservatively assume that number of malicious participants is 50%.

If the witness is chosen randomly at each round, then the probability of privacy being compromised (ie. a single attacker being able to identify the initial source address with the final destination address) will be multiplied by 0.5 at each stage. After 10 rounds it will be $0.5^{10}$ (less than one in a thousand).

Requiring at least one round where you are your own witness (and thus no anonymity was lost) reduces the possibility of lost anonymity except in the degenerate circumstance where all other participants are malicious and coordinated. If the presence of malicious participants is excessive, a reasonable strategy would be to partake in a fixed number of rounds equal to the average number of participants per round. Prior to beginning, one specific round would be selected at random in which oneself were the witness (thereby guaranteeing full privacy for that round, as long as an attacker were not able to guess which round it is in advance). A random non-self witness would be chosen for each other round.

Furthermore, if the number of participants/identities is unlimited, one can begin with several source accounts, and choose one of your own accounts as a trusted partner for another.

On the Ethereum platform, the possibility of entirely automating this process with a ÐApp allows for a sophisticated anonymity mechanism that could even, depending on the number of malign participants, make the owner money. Plausible deniability offers another reason why a user may wish to run the ÐApp: through the  automatic deletion of the secret key a user can honestly state that they have no ability to identify their witness.

## Use cases

Voting is the primary purpose for which this algorithm is designed. Anonymity is a very important precondition to a fair voting scheme; this facilitates the ability to prove that a member of a set did indeed vote without forcing the member to specify what their vote actually was. In this case, the contract implementing this algorithm would restrict the set of possible participants to only those members of the organisation able to vote.

This algorithm could also be used in case of more complicated voting rules. E.g., if shareholders' vote has weight proportionate to the number of shares they hold. In this case there would be multiple source account "slots" allocated to individual members for accordingly larger voting rights.

The algorithm may be used to give privacy regarding ones financial transactions; this has been identified as an important aspect to guaranteeing certain civil freedoms. In that case it is even simpler---there is no need to limit the number of participants. In order to manage arbitrary amounts of funds, different contracts could be deployed which would manage a particular power-of-two amount of Ether.

Anonymity, of course, is a double-edged sword. This algorithm could be used for nefarious purposes as well. Those wishing to conceal the origin of Ether for reasons counter to law-enforcement may use this algorithm to help them coordinate such activity[^apology].

[^apology]: I apologize if this text is stained with my tears: I am weeping now, because I don't know how to prevent the bastards from using my beautiful algorithm for their evil purposes - Vlad.

## Conclusion

We have presented an algorithm which may be used to guarantee restricted anonymity on a fully transparent shared computation environment such as Ethereum, through providing an cryptographically secure, hidden 1:1 mapping between two sets of identities. It is efficient and robust against attacks. Through using an iterative strategy of this algorithm, full anonymity may be guaranteed in all but the most degenerate circumstances.

We have identified a number of high-value use cases which would otherwise be difficult or impractical to do within the Ethereum system.
