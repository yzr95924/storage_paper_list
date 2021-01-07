---
typora-copy-images-to: ../paper_figure
---
sgx-perf: A Performance Analysis Tool for Intel SGX Enclaves
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| Middleware'18 | SGX performance |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
  - understanding the *performance implications* of SGX and the offered programming support is still in its infancy.
    - time-consuming trial-and-error testing and poses the risk of poor performance.
    - current methods (e.g., VTune) cannot support empowering users to implement well-performing applications.

### SGX-Perf

- SGX architecture
  - Trusted Runtime System (TRST) and Untrusted Runtime System (URTS) handle the **enclave transitions** and **call dispatching**.
  - Enclave performance considerations 
    - enclave transitions: 8,600 and 14,000 cycles
      - need to reduce the number of enclave transitions
    - in-enclave synchronization
      - as sleeping is not possible inside enclave, 
        - in-enclave synchronization primitives provided by the SGX SDK implement additional **ocalls** to sleep outside the enclave.
    - enclave paging
      - EPC: one metadata page, its code, the heap and a thread-data page (TCS), stack and state-save-area (SSA) pages for each configured enclave thread. 
      - added enclave transitions to handle page faults 
      - extra computation needed for cryptographic operations

![image-20201128184758932](../paper_figure/image-20201128184758932.png)

- SGX problems and solutions
  - The overhead of using enclaves 
    - the number of enclave transitions during execution and their duration
    - the number of paging events: page fault + encryption
  - Reducing the number of enclave transitions should be **prioritized**.
  - Design trade-offs
    - TCB size and security issue
    - **reordering** should be first considered in the performance optimization.

![image-20201128194505744](../paper_figure/image-20201128194505744.png)

- sgx-perf design 
  - tracing ecalls and ocalls: change the symbols of wrapper codes.
    - logger
    - tracing in-enclave synchronization 

### Implementation and Evaluation
- Evaluation
  - Key question:
    - what is the overhead of running an application with sgx-perf?
      - the overhead of logger
    - can sgx-perf detect optimization opportunities in systems that use Intel SGX?
  - evaluation application
    - TaLos: crypto library
    - SecureKeeper: a key-value store
    - SQLite
    - LibreSSL partitioned with Glamdring
      - automated partition code 

1. performance overhead of logging
2. optimization of enclaves


## 2. Strength (Contributions of the paper)
1. summary identified performance critical factors of SGX
2. present sgx-perf, a collection of tools for high-level dynamic performance analysis of SGX-based application.
> perform fine-grained profiling of performance critical events in enclave
> also provide recommendations on how to improve enclave performance

3. show how to use sgx-perf to improve the SGX-based application performance.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

1. how to optimize the enclave performance?
> asynchronous calls 
> extended memory management support 

2. EPC protection
All enclave memory is **fully and transparently** encrypted as well as integrity protected. 

3. Security enhancements
It is necessary to reduce the attack surface of their interfaces.
> public ecall: can always be called
> private ecall: can only be called during an ocall
>
> > the attacker may change the control path of the execution of the program and gain access to enclave secrets.
