---
typora-copy-images-to: paper_figure
---
DupLESS: Server-Aided Encryption for Deduplicated Storage
------------------------------------------
|       Venue        |       Category       |
| :----------------: | :------------------: |
| USENIX Security'13 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper: 
In cloud deduplication, Message-locked encryption (MLE) is inherently subject to brute-force attacks that can recover files falling into a know set. This paper proposes an key-server based architecture that can provides secure deduplicated storage resisting brute-force attacks.
> The weakness of **convergent encryption** (CE): deterministic and keyless, security only is possible when the target message is too **large** to exhaust. (unpredictable)

- Secure deduplication: Dedup with strong security against untrusted storage 
- Compromise resilience: under client compromise

### DupLESS (Duplicateless Encryption for Simple Storage)
- It contains a group of affiliated clients, a key server
> Goal: make DupLESS work **transparently** with existing Storage Service system. (Site as a layer on top of existing simple storage service interfaces) 

- Oblivious PRF (OPRF) protocol (Server-aided encryption)
$F$: A pseudorandom function (PRF)
$K \leftarrow F(K_s, H(f))$ ($f$ is the file)
> OPRF: server learns nothing, client learns on $K$

- Against online brute-force attack:
Goal: slow down online brute-force trials from attacker controlled clients
> limit clients to send $q$ queries per epoch. 

- System Overview
![1551708732566](paper_figure/1551708732566.png)
> Encrypt and decrypt files
> Handle file names and paths
> Run Transparently: low overhead, still can run when KS is down, no client-side state

### Implementation and Evaluation
- DupLESS client: python, support **Dropbox** and **Google Drive**. (command-line interface)
- Evaluation
> 1. Bandwidth overhead: DupLESS bandwidth overhead compared to plain Dropbox
> 2. Retrieval latency: vary file size, compare with plain Dropbox and Covergent Encryption.
> 3. Storage Overhead: DupLESS storage overhead compared to dedup over plaintexts

## 2. Strength (Contributions of the paper)
1. The system design is very considerable. When authors design DupLESS, they consider deduplication, compromise resilience and brute-force attack resilience.
2. To achieve low performance overhead, this paper also considers the optimization in client-to-KS OPRF protocol.
3. To guarantee the performance, DupLESS aims to work as a transparent layer over the storage service system.
## 3. Weakness (Limitations of the paper)
1. Compared with plain Dropbox or Google Drive, DupLESS incurs the higher overhead on storage overhead (encrypted user key from user) so as to gain the improvement in security.
## 4. Future Works
1. How about do the deduplication over the metadata (e.g., the *key* used to encrypt data and be encrypted by user's private key)? Although the size of this part of data may be very small, they also store in back-end storage service.
2. Can it  leveage the characteristic of the data workload to further optimize the deduplication process?
