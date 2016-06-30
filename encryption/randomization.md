### Block Cypher Randomization Algorithm

**Author**: Vlad Gluhovsky

**License**: The Unlicense (<http://unlicense.org/>)


Here is my proposal to enhance block cyphers by introducing additional **random** component (not pseudorandom) in them, for the purpose of making cryptanalysis more difficult for any potential attacker. It is suitable for any block cypher (BlowFish, Salsa, etc.).

### Encryption Algorithm

- Generate additional pseudorandom gamma. The generation algorithm should be able to produce the gamma of arbitrary length. It could be just a simple hash-chain. For simplicity, we can use the same password for block cypher and for gamma generation, just different algorithms.
- Create a buffer with arbitrary capacity. There is a relationship between the buffer and the gamma: one **bit** of the gamma correspond to one **byte** of the buffer.
- Fill the buffer according to the following rule. If next bit of gamma is set, copy one byte from the plaintext, else copy one byte of random data.
- Encrypt the buffer with a standard block cypher.

### Decryption Algorithm

- Decrypt the buffer with a standard block cypher.
- Generate gamma.
- Delete the bytes that correspond to unset bits in gamma.

### Discussion

It is not yet established if this algorithm will indeed enhance the strength of the block cyphers. It is subject to further research and discussion. I think that it would definitely make certain attack models more difficult (e.g. ciphertext-only attack or chosen-plaintext attack). One thing we can say for certain, though: it can not decrease the cryptographic strength of the underlying block cyphers, especially if different passwords are used for standard block cypher encryption and for gamma generation. At worst, it could only be useless. However, even in this case, it could still be useful for steganographic purposes. There is no way to know if the additional random data are indeed random or contain some encrypted message. If a lot of people will use this algorithm by default, it will provide very good plausible deniability for those who have the need for steganographic solutions to protect their privacy. It could be particularly useful in countries where oppressive governments impose harsh penalties (even capital punishment) for victimless crimes. 

### Feedback 
by Ray Dillinger <bear@sonic.net>

That's interesting as a simple primitive for multiplexing and demultiplexing garlic-routed messages. Admixture of many encrypted channels in a single stream, where in order to separate and decrypt the stream you need the stream key, but in order to read any part of a channel you'd also need the channel key. The cutover points between different data channels would be determined by PRNG generated from the shared stream key. The endpoints, knowing the stream key because they'd established the stream in the first place, could separate the encrypted channels, remix them in different combinations, and redistribute them using different treams. Of course it's recursive; any "channel" in a given stream might contain a stream of its own. That mutation of the original idea might be a very good one; it could enable continuous-streaming garlic-routed channels, with cutin/cutout of particular channels on a realtime basis becoming stupidly simple. And it doesn't waste CPU/bandwidth on encrypting bits we don't actually care about; it's just a simple way to multiplex/demultiplex channels over a shared encrypted stream and doesn't spend (much) more CPU/bandwidth than stream encryption normally would.