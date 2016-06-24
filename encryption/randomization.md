### Block Cypher Randomization

Author: Vlad Gluhovsky

Here is my proposal to enhance block cyphers by introducing a random component (not pseudorandom), for the purpose of making cryptanalysis more difficult. It is suitable for any block cypher (BlowFish, Salsa, etc.).

### Encryption Algorithm

- Generate additional pseudorandom gamma. The generation algorithm should be able to produce the gamma of arbitrary length. It could be just a simple hash-chain. For simpicity we can use the same password for block cypher and for gamma generation, just different algorithms.
- Create a buffer with arbitrary capacity. There is a relationship between the buffer and the gamma: one **bit** of the gamma correspond to one **byte** of the buffer.
- Fill the buffer according to the following rule. If next bit of gamma is set, copy one byte from plaintext, else fill this byte with random data.
- Encrypt the buffer with a standard block cypher.

### Decryption Algorithm

- Decrypt the buffer with a standard block cypher.
- Generate gamma.
- Delete the bytes that correspond to unset bits in gamma.

### Discussion

It is not yet established if this algorithm will indeed enhance the strength of the block cyphers. It is subject to further research and discussion. However, one thing we can say for certain: it can not do any harm, especially if using different passwords for standard block cypher encryption and for gamma generation. At worst, it could only be useless. 

However, in this worst case it could still be used for steganographic purposes. There is no way to know if the additional random data are indeed random or contain some encrypted message. If a lot of people will use this algorithm by default, it will provide very good plausible deniability for those who will have the need for steganographic solutions to protect their privacy.
