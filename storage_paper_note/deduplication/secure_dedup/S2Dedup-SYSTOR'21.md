---
typora-copy-images-to: ../paper_figure
---
S2Dedup: SGX-enabled Secure Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SYSTOR'21 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - current solutions are built with strict designs that cannot be adapted to storage service and applications with different **security** and **performance** requirements
  - TEE can be leveraged to `aid the process of secure deduplication`
    - as it allows for sensitive data to be handled in its original form (i.e., plaintext) at an untrusted storage server
      - TEE is still vaguely explored for deduplication
- Limitations of state-of-the-art approaches
  - TED-Eurosys'20: estimated frequency counter can lead to the speculation that a block has a higher number of duplicates than in reality
    - lead to lower deduplication gains
  - Epoch-Dedup-CLOUD'17: needs proxies that add extra computation and network operations overhead 
    - decrease the storage performance
- open-source link
  - https://github.com/mmm97/S2Dedup

### S2Dedup

- Design goals
  - enable multiple schemes that can be adapted to the performance and security requirements
    - for different applications
  - with stronger security guarantees than `deterministic ones`
  - avoid the network performance overhead imposed by auxiliary trusted remote servers
- Overview
  - main idea: leverage trusted hardware technologies to enable `cross-user privacy-preserving deduplication` at third-party storage providers
  - threat model:
    - a strictly stronger adversary that gain access to the server
      - can observe access patterns of the storage service and the management of deduplication data and metadata.
  - architecture:
    - ![image-20210615154001965](../paper_figure/image-20210615154001965.png)
    - key points:
      - inline fixed-size block (4KB) deduplication
      - **hashing** and **deduplication** are performed exclusively at `the server-side`.
        - avoid the side-channel issues 
      - rely on the enclave to perform the **hash computation** step
        - using a specific cryptographic **hash key**
      - re-encrypt the unique blocks with a `a single universal encryption key` for data storage
        - resort to SGX for re-encryption 
        - without requiring clients to share their encryption keys or derive them from the content being written as MLE
- Secure deduplication schemes
  - plain security 
    - the adversary can play the role of a client to test for duplicates, querying the system with values and checking if deduplication occurred
  - epoch based
    - the enclave automatically changes the **hash key** based on epoch duration (e.g., a threshold)
      - keyed hash functions over the same data with different keys will produce different digests and disables deduplication
    - the server is no longer capable for indefinitely testing for duplicates
  - estimated frequency based
    - follow the same design as TED but uses trusted enclave
  - epoch and exact frequency based
    - combine above both
    - use an in-memory hash table at the secure enclave that maps blocks' hashes and the **exact** frequencies
      - use epoch-based approach to set up a temporal boundary  
        - a new epoch is created when the hash table reaches the enclave space limitation, and clearing the hash table information

### Implementation and Evaluation

- Implementation
  - C, SGX SDK, SPDK
  - AES-XTS block cipher mode, HMAC-SHA256
  - index: consider two cases to evaluate I/O impact
    - Glib for in-memory index
    - LevelDB for persistent index
- Evaluation
  - workloads
    - DEDISbench: a disk I/O block-based benchmarking tool
    - real traces
      - FIU I/O dedup
  - Compared with:
    - baseline: plaintext data deduplication
    - plain, epoch, estimated, exact
  - I/O performance
  - the impact of resource consumption (i.e., RAM, CPU, network)
  - space savings

## 2. Strength (Contributions of the paper)

- the first modular solution supporting multiple secure deduplication schemes
  - enable different trade-offs in terms of **security**, **performance**, and **space savings**.

## 3. Weakness (Limitations of the paper)

- Do not use the number to compare the security between different schemes

## 4. Some Insights (Future work)

- where happens the encryption?
  - standard practices suggest that users should encrypt their data **before** outsourcing it to third-party storage services
  - CE reveals matching ciphertexts, these schemes provide considerably `less` security guarantees than standard encryption schemes
    - cite TED-Eurosys'20: hinders their usability in scenarios handling `sensitive` data
- epoch in deduplication
  - the information leakage can be reduced by performing deduplication in `epochs`, which ensures that an adversary can `only infer duplicates within the same epoch`
    - the epoch needs to be `synchronized` across all clients
      - it needs `a secure proxy or TEE on the storage server` to control the change of epoch.
      - the proxies add extra computation and network operations in the critical I/O path
        - decrease storage performance 

- why TED is bad?
  - may overestimate the number of duplicates actually found for a given block
    - miss some deduplication opportunities
  - it does not prevent a malicious adversary from knowing exactly the number of references for chunks with `low duplication counts`
- AES-XTS block cipher mode
  - support random access to the encrypted data