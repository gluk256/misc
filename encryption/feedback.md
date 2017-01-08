Feedback on Block Cypher Randomization Algorithm
by Ray Dillinger <bear@sonic.net>

The proposal is the addition of *random* salt to the text to be
encrypted, with the locations of each octet of random salt determined by
a stream of *pseudorandom* bits generated based on a secret key;
possibly the same key used in the encryption.  The recipient of the
message can generate the same stream of pseudorandom bits and therefore
has the ability to subtract the random data from the decrypted message.

It's a sound construction on the whole for security purposes, and it
might be practical as a way to protect against certain types of protocol
attacks which are far more prevalent than mathematical cryptography
attacks.

Otherwise it won't be used because it makes encryption take longer and
makes the transmission of encrypted text require more time and battery
power.  Cryptographers at present are mostly satisfied about
mathematical cipher security, and therefore more concerned about
bandwidth and CPU time on servers and bandwidth and battery life on
portable devices.

At this point pretty much anyone who understands Feistel cipher
construction can make a *secure* block or stream cipher, simply by
applying brute force and using some ungodly number of iterations on
pretty much any nonlinear round function executed on large blocks. But
it requires serious math chops to make a secure cipher which is also
efficient and flexible for use in protocols.

Second; what you propose is a *very* good protection against
chosen-plaintext attacks (due to salt destroying patterns in the chosen
plaintext) and a fairly good protection against a large common class of
protocol attacks that relies on knowing the locations of particular
pieces of plaintext in the datagrams (because the randomization of salt
locations makes the exact location of plaintext w/r/t block boundaries
of block ciphers and/or the locations of particular pieces of plaintext
in structured messages in stream ciphers unpredictable).

The protection it affords against the latter class of attacks makes them
probably only about one order of magnitude more difficult to exploit,
more on large messages.

Counting against its security is the fact that it causes the size of the
cryptogram to be increased by a factor of two.  This increases the
amount of ciphertext an opponent has to work with, making a difficult
class of attacks that relies on distinguishing statistical properties of
a ciphertext stream slightly easier.  Offsetting the fact though is that
using such statistical properties to recover any information about a
ciphertext stream half of which is random, is more than twice as hard.
Therefore the only class of such attacks that really matters are
key-recovery attacks against long-term keys that will be used in other
messages.  Key recovery attacks are the most rare and difficult subclass
*of* the aforementioned difficult class, useful only against a type of
key which has become quite rare given public-key encryption and
Diffie-Hellman key negotiation.

In security terms, the marginal weakening against key-recovery attacks
loses less than is gained by the strengthening against chosen-plaintext
cryptographic attacks, and FAR less than is gained by strengthening
against known-plaintext-location protocol atttacks.

It could be strengthened probably by another order of magnitude against
chosen-plaintext attacks on ciphers and known-plaintext-location attacks
on protocols, with a modification.  As your proposal stands you're using
the stream 'gamma' bit-by-bit to decide whether to insert one byte of
salt or one byte of plaintext.   The attacker still knows that the
alternation between plaintext and ciphertext will be on byte boundaries
only.   Instead, consider using gamma a byte at a time: five bits to
decide how many *bits* of salt to use before switching to plaintext, and
three bits to decide how many *bytes* of plaintext to send before
switching back to salt.  Counting against the modification, however,  is
the primary reason why this method won't be used much in the first
place; bit shifts are an extra step that make the modified method take
even more time and use even more battery power.

===

That's interesting as a simple primitive for multiplexing and demultiplexing garlic-routed messages. Admixture of many encrypted channels in a single stream, where in order to separate and decrypt the stream you need the stream key, but in order to read any part of a channel you'd also need the channel key. The cutover points between different data channels would be determined by PRNG generated from the shared stream key. The endpoints, knowing the stream key because they'd established the stream in the first place, could separate the encrypted channels, remix them in different combinations, and redistribute them using different treams. Of course it's recursive; any "channel" in a given stream might contain a stream of its own. That mutation of the original idea might be a very good one; it could enable continuous-streaming garlic-routed channels, with cutin/cutout of particular channels on a realtime basis becoming stupidly simple. And it doesn't waste CPU/bandwidth on encrypting bits we don't actually care about; it's just a simple way to multiplex/demultiplex channels over a shared encrypted stream and doesn't spend (much) more CPU/bandwidth than stream encryption normally would.