---
typora-copy-images-to: paper_figure
---
Proofs of Ownership in Remote Storage Systems
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ACM CCS'11 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Client-side dediplication allows an attacker to gain access to arbitrary-size files of other users based on a very small hash signatures of these file.
To overcome it, it introduces the notion of proofs-of-ownership (PoWs), which lets a client efficiently prove to a server that that the client holds a file, rather than just some short information about it.

This paper only focuses on **file-level** deduplication.
### Proofs of Ownership
- A New Attack
**Root cause**: By accepting the hash value as a "proxy" for the entire file, the server allows anyone who gets the hash value to get the entire file. (via asking the recovery from the storage service)
> 1. by learning a small piece of information about the file, namely its hash value, an attacker can get the entire file from the server.
> 2. a very short piece of information that represents the file.
> 3. there are several kinds of attack based on this security vulnerability. 

- Proofs of Ownership (PoWs)
**Goal**: design a solution where a client proves to the server that it indeed has the file.
Requirements:
> 1. **public** hash function (procedure must be public)
> 2. the protocol run between the server and client must be **bandwidth efficient**
> 3. Sever constraints: the solution must allow the server to store only ax extremely **short information** per file.
> 4. Client constraints: the client can compute the proof by making **single pass** over the file.

**Security Definition**:
As long as the file has a lot of min-entropy, the attacker has only a small chance of convincing the server.

- Solutions
1. General solution: Erasure Coding + Merkle tree

2. More efficient solution: using universal hashing






### Implementation and Evaluation

## 2. Strength (Contributions of the paper)
1. This work puts forward the notion of proof-of-ownership by which a client can prove to a server it has a copy of a file without actually sending it.


## 3. Weakness (Limitations of the paper)

## 4. Future Works
