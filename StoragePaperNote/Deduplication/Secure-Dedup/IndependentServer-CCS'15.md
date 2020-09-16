---
typora-copy-images-to: ../paper_figure
---
Secure Deduplication of Encrypted Data without Additional Independent Servers 
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CCS'15 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Current solutions either use 
> 1. convergent encryption: susceptible to offline brute-force attacks
> 2. require the aid of additional independent servers, which is very diffcult to meet in commerical contexts. (online brute-force)

This paper argues that those schemes with additional independent servers setting are unrealistic in commercial setting and can be bottlenecks for both security and performance.

This paper propses a secure cross-user deduplication scheme that supports client-side encryption **without requiring any additional independent servers**.
> based on PAKE (password authenticated key exchange)

### PAKE
- Goal: allow a client uploading an existing file to securely obtain the encryption key that was used by the client who has previously uploaded that file.
> via using a PAKE-based protocol to compute identical keys for different copies of the same file.

- Password Authenticated Key Exchange
![1561707845616](../paper_figure/1561707845616.png)

Its deduplication scheme uses the SPAKE2 protocol, which is secure in the concurrent setting and random oracle model.
> one client runs multiple PAKE protocol instances with other clients.
> ![1561708816690](../paper_figure/1561708816690.png)

In this work, clients use the hash values of their files as their respective "passwords".

- Design goal
1. Security goal: brute-force attack
2. Functional goal: maximize deduplication effectiveness, minimize computational and communication overhead


- Deduplication protocol
![1561714450649](../paper_figure/1561714450649.png)

1. Client 
Before uploading a file $F$, the uploader $C$ calculates both the cryptographic hash and the short hash, and send the short hash to the storage server.

2. Sever 
finds some checker clients with the same short hash. Then let the client run the PAKE protocol with those checkers. (input is the cryptographic hash)



### Implementation and Evaluation
- Evaluation:
1. Dataset
use a dataset comprising of Android application prevalence data to represent an environment with media files.
2. Dedup. percentage vs rate limits 
not perfect deduplication

## 2. Strength (Contributions of the paper)
1. This paper presents the first single-server scheme for cross-user deduplication
> enables the client-side semantically secure encryption, proving its security in the malicious model.

2. This method can provide the better security guarantees without introduing an identity server.


## 3. Weakness (Limitations of the paper)
1. This paper assumes there are enough online clients who have uploaded files with the required short hash. But this assumption is not very solid.

2. This paper uses the additively homomorphic encryption in its protocol, and the overhead of PAKE is not very low, which makes this method 

## 4. Future Works
1. This paper argues that per-client rate limiting is not fully effective against such online brute-force attack since an adversary who may compromise multiple clients.
> this paper leverages the per-file rate limiting strategy to prevent online brute-force attack.

2. This paper argues that a compromised storage server can easily mount an offline brute-force attack on the hash if a file is predictable.
> in this paper, it lets a client send a short hash, and due to the high collision rate, the storage server cannot use this short hash to reliably guess the content of files offline.
> 13 bits long hash

3. This paper mentions the issue of user involvement. And it argues that it is burdensome for average users to let them aware which files are sensitive. And it also foregoes deduplication of sensitive files together

4. This paper mentions that very small sacrifice in deduplication ratio is offset by the significant advantage of ensuring user privacy without having to use independent third party servers.