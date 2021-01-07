---
typora-copy-images-to: ../paper_figure
---
TaLoS: Secure and Transparent TLS Termination inside SGX Enclaves
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| arxiv'17 | SGX communication |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
  - design a drop-in replacement for existing transport layer security (TLS) libraries that protects itself from a malicious environment
    - running inside an Intel SGX trusted execution environment.

- Main idea
  - TaLoS protects *private keys* and *session keys* from a malicious environment.

- Main goals
  - security and privacy: resilient to different threats
  - ease-of-deployment: be easy to deploy with existing applications
    - no changes to the client implementations
  - performance overhead: impose a low performance overhead with respect to native application execution.

### TaLoS

- Intel SGX background
  - enclave code is permitted to access enclave and non-enclave memory.
  - when EPC is flushed to DRAM or disk, they are **transparently** encrypted and integrity protected by on-chip memory encryption engine.

- TaLoS TLS termination
  - place the following parts inside the enclave:
    - private keys
    - session keys 
    - code related to the TLS protocol implementation (SSL_read(), SSL_write())
  - non-sensitive code and data are placed outside of the enclave for performance reasons.

![image-20201220224404400](../paper_figure/image-20201220224404400.png)

- Enclave TLS implementation
  - Secure callbacks: use ocalls rather than regular function calls.
    - store the address of the callback function
    - generate a trampoline function to perform an ocall into the outside application code 
  - Shadowing (shadow data structure)
    - TaLoS maintains a sanitized copy of the SSL structure outside the enclave, with all sensitive data removed.

- Reducing enclave transitions 
  1. use a pre-allocated memory pool to manager the small objects 
    - Instead of performing ocalls to allocate non-sensitive objects from within the enclave.
  2. avoids ocalls to pthread by using the thread locks implementation provided by the SGX SDK
  3. use SGX random number generator inside the enclave to avoid ocalls to the random system call.
  4. storing application-specific data written to TLS data structures outside the enclave 
    - reduce the number of ecalls

- Reducing transition overhead
  
  - asynchronous enclave transitions



![image-20201221014051897](../paper_figure/image-20201221014051897.png)

### Implementation and Evaluation

- Implementation
  - TaLoS exposes 205 ecalls and 55 ocalls
    - 282,200 LoC

- Evaluation
  - Configuration:
    - Using Apache and Squid
  - Enclave TLS overhead
    - CPU is the bottleneck
    - TaLoS incurs a 23% performance overhead
  - Impact of asynchronous calls
    - increase the performance by at least 57%
  - Scalability
    - increase the number of CPU cores
## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

1. The difference between the SGX-SSL
Both SGX-SSL and mBedTLS-SGX require the entire application to execute inside the SGX enclave.

