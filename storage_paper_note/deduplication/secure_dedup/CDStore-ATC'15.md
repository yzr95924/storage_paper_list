---
typora-copy-images-to: paper_figure
---
CDStore: Toward Reliable, Secure, and Cost-Efficient Cloud Storage via Convergent Dispersal
------------------------------------------
@ATC'15 @Cloud Deduplication
[TOC]

## 1. Summary
### Motivation of this paper: 
- Existing secret sharing algorithms prohibit storage savings achieved by **deduplication**. Thus, this paper tries to design a new multi-cloud storage system, which can provide a unified cloud storage solution with reliability, security, and cost efficiency guarantees. (Core: enable secret sharing with deduplication)


### CDStore
- Reliability
> 1. Fault tolerance in cloud server side (cloud storage providers, CDStore servers) $\rightarrow$ Erasure Coding
> 2. Fault tolerance in cloud client side $\rightarrow$ offloading metadata management to the server side 

- Security
> 1. Exploits multi-cloud diversity (as long as a tolerabel number of clouds are uncompromised.)
> 2. Two-stage deduplication to avoid insider side-channel attacks launched by malicious users.

- Cost Efficiency
> 1. use deduplication to reduce both bandwidth and storage costs.

#### Convergent Dispersal
Goal: two secrets with identical content must generate identical shares. (make deduplication possible)
> replacing the embedded random input with a deterministic cryptographic hash drived from the secret.

- CAONT-RS (a new instantiation of convergent dispersal)
> 1. Improve performance: replaces the Rivest's AONT with another AONT based on **optimal asymmetric encryption padding** (OAEP) (small size words $\rightarrow$ a large-size, constant-value block)
>
> 2. Support deduplications: replaces the random key in AONT with a deterministic cryptographic hash derived from the secret. (preserves **content similarity**)
>![1548039339341](paper_figure/1548039339341.png)

- Two-Stage Deduplication
Goal: reduce the storage overhead and upload overhead, as well as defend side-channel attacks.
> Client side: before a user uploads data to a cloud, it first generates fingerprints of data, and then checks (queries) with the cloud by fingerprint for the existence of any deduplicate data that has been uploaded by any user.

> Server side: After CDStore server receives the shares from clients, it generates a fingerprint from each share (**re-compute again**, instead of the use the one generated by client.). And check with its deduplication index again.
> The reason: to prevent the side-channel attack to gain unauthorized access to the share

### Implementation and Evaluation
- Some implementation details
> 1. To reduce network I/Os: batch the shares to be uploaded to each cloud in a 4MB buffer.
> 2. Variable-size chunking: Rabin fingerprinting
> 3. To provide the reliability: offloading the metadata management in server side. distribute metadata across all CDStore servers for reliability.
> 4. Multi-threading: intensive encoding/decoding operations at secret level, utilize the network transfer bandwidth. 

- Evaluation
> 1. Encoding speed in client side (compare with AONT-RS, CAONT-RS-Rivest)
> 2. Deduplication efficiency (deduplication saving)
> 3. Transfer speed: Upload speed, download speed. (Two cases: duplicate data, trace-driven )
> 4. Cost Analysis: estimate the monetary costs using the pricing models of Amazon EC2 and S3.

## 2. Strength (Contributions of the paper)
- This paper proposes CAONT-RS, which can improve the performance of AONT-RS, and also can allow deduplication by using deterministric hashes.
- This paper also implements the CDStore system with a new instantiation of convergent dispersal, which can achieve higher throughput than its prior approach.
- It also conducts a series of experiments and cost analysis to prove its design.

## 3. Weakness (Limitations of the paper)
- In its two-stage deduplication, the server needs to re-compute the fingerprint to defend the attacker, I think the overhead of this scheme is a lttile bit high. 
- CDStore does not consider how to further reduce the overhead of deduplication metadata.

## 4. Future Works
- As mention in this paper, I think one pential issue that can be done is to consider other different techniques to further deduplicate with the metadata.