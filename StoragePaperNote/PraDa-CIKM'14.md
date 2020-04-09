---
typora-copy-images-to: ../paper_figure
---
PraDa: Privacy-preserving Data Deduplication as a Service
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CIKM'14 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
This paper argues the data cleaning is a labor-intensive and complex process.
> Is extremely challenging (cleaning for large scale data has grown difficult)
> A solution is to outsource the data to a third-party data cleaning service provider. (cloud based data cleaning services)

In this paper, it regards the data deduplication as the data cleaning problem, and investigates how to protect the privacy information when outsourcing the data to a third-party service provider.
> the client encodes its data and sends the encoded data to the server.
> the server is untrusted, may try to decode the received dataset to infer the private information.

- Threat
1. the attacker may have the frequency distribution information of the outsourced data (may launch frequency analysis)
2. the attacker knows the details of the encoding scheme (may launch known-scheme attack)

Add a **salt** can prevent frequency analysis attack but it may disable to find duplicated records form the encoded data.

- The difference between this work and data deduplication
In this work, it emphasizes on the data similarity of records
> their similarity according to some distance measurement metrics is no less than a given threshold $\delta$ (near-duplicates)

### PraDa
- Goal 
It enables the client to outsource her data as well as data deduplication needs to a potentially untrusted server.
> enables the server to discover near duplicated records from the encoded data

- Locality-sensitive hashing based approach (LSHB)
Idea: map the strings to locality-sensitive hashing (LSH) values.
> LSH is a set of hash functions that map objects into several buckets such that similar objects share a bucket with high probability.
> Any two similar objects will have the same LSH value with high probability.
> **MinHash** function is the LSH function family.


### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
