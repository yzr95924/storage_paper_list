---
typora-copy-images-to: ../paper_figure
---
Compare-by-Hash: A Reasoned Analysis
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ATC'06 | Hash |
[TOC]

## 1. Summary
### Motivation of this paper
This paper investigates the digest of a cryptographic hash function is equal on two distinct files.
> instead of comparing them byte-by-byte.
> can quickly check the files for equality by once again comparing only a few bytes rather than reading through them byte-by-byte.

In rsync and LBFS, the goal of the designers was not to provide **security** via the use of cryptographic hash functions.

This paper intends to against the work of Henson in HotOS'03, and suggests that it is certainly fine to use a 160-bit hash function like SHA1 with compare-by-hash.


### Method Name
- The basic of hash functions
1. collision-resistant
2. inversion-resistant
3. second-preimage-resistant

In the context of compare-by-hash, collision resistant is the main concern.
> if it is given a list of $b$-bit independent random strings, it is expected to see the collision after about $2^{\frac{b}{2}}$. (**birthday bound**)
> For example, for MD5, there should begin showing collisions after $2^{64}$ hash values.

- The central theme in Henson's paper 
Her opposition to the notion that digests can be treated as unique ids for blocks of data.

- Key property 
> Most file-system data are in fact **not uniform** over any domain.
> SHA-1 does a very good job at mapping corelated inputs to uncorrelated outputs. (**differential cryptanalysis**).


- The probability of collision

- Attack model 
1. Bad luck attacker: 
The chance that two files will have the same hash is about $2^{-160}$ for a 160-bit hash function like SHA-1.

2. An intelligent attacker
try to cause collision, may substitute the blocks with same hash value.

Compare-by-hash holds up well in both attack models.
> In typical compare-by-hash setting there is no adversary

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. Two practical applications of compare-by-hash 
> rsync, Low-Bandwidth File System (LBFS)

2. The security of SHA-1
> this paper estimates it would cost 80,000,000 USD and 2 years to find a collision in SHA-1

3. Cryptographic hash function in digitial signature 
> a document being digitally signed is first hashed with a cryptographic hash function, and the signature is applied to the hash value
> applying a computationlly-expensive digital signature to a large file would be prohibitive.
