---
typora-copy-images-to: ../paper_figure
---
Privacy-Preserving Data Deduplication on Trusted Processors
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CLOUD'17 | Deduplication SGX |
[TOC]

## 1. Summary
### Motivation of this paper
Revealing ownership and equality information of the outsourced data to untrustworthy parties has serious privacy implications.

- This paper leverages the SGX to design a privacy-preserving data deduplication protocol that protects not only the confidentiality of the outsourced data, but also their ownership and equality information.

### SGX Deduplication
- Basis of SGX
1. SGX provisions the protected execution environments  (a.k.a, enclaves). Each enclave is associated with a region on physical memory
> 90MB
> code and data inside the enclave is protected by the processor
> enclave may access the enclave memory as well as memory outside the enclave region.

2. Enclaves cannot directly execute OS-provided services such as I/O.
> a communication channel between the enclave code and the untrusted environment (e.g., OS) is required to service OS-provided functions.

3. Each such SGX processor has a unique secret burned into its fusion.
> refer it as **seal key**.

- Architecture
  - Proxy-based Architecture (three-tier setting)
  > cloud storage provider, the local proxy, the client

  - Duplicates uploaded by affiliated clients are detected and removed at **proxy**
  - Cross-enterprise deduplication is performed on **Storage sever S**
client-proxy-storage server
> offers almost similar bandwidth savings to that of client-side deduplication solutions, but does not admit the leakage wherein a client can learn if a file is already stored on the server.
![1570362198248](../paper_figure/1570362198248.png)
> **Storage server $S$** and **enterprise proxies $P$** are equipped with SGX-enabled processors.

- Threat Model
The attacker can gain complete control over the operating systems and other privileged softwares of storage servers and proxies.
> However, the attacker cannot violate SGX guarantees.

- Design Goal 
  - Data confidentiality
  - Ownership information
  - Equality information
  - Performance requirements: keeping communication and computational overhead low.
  
- Whole Design
1. CUpload (client)
> client interacts with **PEnclave** to use blind signature scheme to get the encryption key.
> file-level deduplication, using **deterministic encryption**.
> Rate-limiting

2. PDedup (proxy)
> perform a **privacy-preserving compaction** to remove duplicate, then uploads the deduplicated data to $S$.
> prevent traffic analysis: pads the traffic from PEnclave to SEnclave following **differential privacy**.
>
> > translate the Laplace noise to a corresponding number of chunks.

### Implementation and Evaluation
- Implementation
  - around 1000 LOC C code, 90MB enclave
  - Intel Skylake processors 

- Evaluation
  - Dataset: Debian Popularity Contest (treat each package as a file and its installation as an upload of that file)
  - Goal: to show the performance overhead is low.

1. upload latency (vary file size)
> compare with uploading the plain files of the same size.

2. break down
> encryption time, key derive time


## 2. Strength (Contributions of the paper)

1. It proposes three-tier deduplication architecture 
> save the bandwidth, yet does not admit the client-side deduplication leakage on file existence

2. leverage SGX in proxy and storage server to protect **confidentiality**, **ownership** and **equality information** of the outsourced data against various adversaries.

3. implement a prototype and conduct experiments to show it incur low overhead
> in comparison with conventional deduplication solutions


## 3. Weakness (Limitations of the paper)
1. This method gives up the storage efficiency since it only consider two-stage deduplication and file-level deduplication, both of them are not exact deduplication. This paper can provide a deep analysis to show the loss of deduplication ratio.

## 4. Some Useful Insights

1. This paper argues that encryption only protects the confidentiality of the files at rest, while sensitive information can still be inferred from their metadata (e.g., ownership and equality information)
> How to combine SGX with encrypted deduplication?

2. This papers use **privacy-preserving compaction** to realize data deduplication instead of table lookup. This provides a new way to achieve data deduplication in SGX environment.

3. This paper mentions the issue that
> successful retrieval of an outsourced record reveals its ownership information.

It can further prevent this leakage by using Oblivious RAM or Private Information retrieval.

> how to mitigate its non-trivial performance overhead