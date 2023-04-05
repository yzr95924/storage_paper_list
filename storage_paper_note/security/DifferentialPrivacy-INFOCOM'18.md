---
typora-copy-images-to: paper_figure
---
Differentially Private Access Patterns for Searchable Symmetric Encryption
------------------------------------------
|   Venue    |       Category       |
| :--------: | :------------------: |
| INFOCOM'18 | Differential Privacy |
[TOC]

## 1. Summary
### Motivation of this paper
This paper proposes  to protect searchable symmetric encryption (SSE) against access-pattern leakage with a form of access-pattern obfuscation (APO). 
It is a framework which can built over any given SSE scheme.

A basic workflow of SEE:
![1557997548672](paper_figure/1557997548672.png)

This work aims to defend query recovery attacks:
The goal of a query recovery attack is to recover the content of a query (i.e., the plaintext keyword) issued by the client to server.

### Access-Pattern Obfuscation
Design goal: provide an access-pattern obfuscation mechanism that is compatible with existing SSE schemes.
> add false positives and false negatives to the search results.
> **false positive**: the server returns some documents that do not match the query (fake documents).
> **false negatives**: the server does not return some documents that match the query (violate the correctness of the SEE scheme)

- How to handle correctness issue of false negatives
This paper introduce redundancy to the document to collection using **erasure codes**.
> each document $\rightarrow$ multiple shards
> the collection of all shards is encrypted and outsourced to the remote server.

It argues allowing a probability that some matching documents may not be returned is essential to archive a provable privacy guarantee such differential privacy, and a fairly high recall rate, say 99.99%, can be useful in practice already.

- Setup Phase
1. For each document $D$, it extracts its keyword list $W$.
2. Applying erasure coding to generate $m$ shares of $D$, append the keyword list for each share.
3. For each share and its keyword list, adopt the access-pattern obfuscation mechanism
> some shares of the matching documents will not be returned in response to a query

- Search Phase
1. Given a search keyword $w$, the resulting search token is sent to the server.
2. The received shares are decrypted.

- $d​$-Private Access-Pattern Obfuscation
The goal of this privacy definition is to guarantee that for access patterns that are **similar**, the obfuscated access patterns generated from them are **indistinguishable**.
> $x=111000$, $x^{'}=110100$, obfuscated access pattern $y=110001$ is observed with high probability, the adversary cannot tell whether $x$ or $x^{'}$ was the original access pattern.

An access-pattern obfuscation mechanism probabilistically convert an access-pattern vector to another access-pattern vector. 
> the intuition of $d$-privacy is that similar access patterns generate similar obfuscated access patterns.
> So from an obfuscated access pattern, it is different to infer its original access pattern.

![1558079145575](paper_figure/1558079145575.png)


### Implementation and Evaluation
- Security Evaluation 
1. The baseline IKK attack
> the adversary's unawareness of the existence of mitigation methods.

2. The improved IKK attack
> further assume the adversary knows the existence of its defenses.
> This is possible when the shards of different original documents are **different in size**. (This point is very important)

- Performance Evaluation 
1. Storage and Communication Overhead
2. Precision
3. Runtime Overhead: access latency

## 2. Strength (Contributions of the paper)
1. It proposes  d-privacy for access pattern of general SSE schemes
2. It designs a d-private access-pattern obfuscation mechanism which is compatible with existing SSE schemes
3. It also implement a prototype of the proposed obfuscation mechanism.

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. This paper provides a good insight on how to leverage erasure coding to achieve differential privacy, and this can be used in other scenarios. How to use in secure deduplication?

> Mainly used to hide access-pattern