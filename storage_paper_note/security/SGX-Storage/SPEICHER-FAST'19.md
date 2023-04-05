---
typora-copy-images-to: ../paper_figure
---
SPEICHER: Securing LSM-based Key-Value Stores using Shielded Execution
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'19 | SGX-DB |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - Persistent KV stores have become a fundamental part of the cloud infrastructure.
    - The security vulnerabilities in third-party cloud pose a serious threat to storage system
    - securing a storage system is quite challenging, since various layers in the system stack.
  - SGX provides an appealing approach to build secure systems.
    - aims to provide strong security properties using the enclave.

- Challenge
  - Shielded execution are primarily designed for securing "**stateless**" in-memory computations and data.
    - not sufficient for building a secure storage system.
  - how to extend the trust beyond the "secure, but stateless" enclave memory region to the "untrusted and persistent" storage medium, while ensuring that the security properties and preserved in the "stateful setting" 
    - Even across the system reboot, migration, or crash.

- Goal: confidentiality, integrity, and freshness.



### SPEICHER

- Threat model
  - In addition to the standard SGX threat model, it also considers the security attacks that can be launched using an **untrusted storage medium**.
  - The adversary can control the entire system software stack, including the OS or hypervisor, and is able to launch physical attacks
    - performing memory probes.

- Design challenges 
  - Limited EPC size: the paging incurs high performance overheads.
  - Untrusted storage medium: need to extend the trust to the untrusted storage medium.
  - Expensive I/O syscall: incur higher performance overhead. (TLB flushes, security check)
  - Trusted counter: 
    - the SGX counters are impractical to design a storage system.

- System architecture
![image-20201110121332322](../paper_figure/image-20201110121332322.png)
  
  -  Controller
    - The TLS channel is terminated inside the controller (inside the enclave)
    - The controller performs the remote attestation service to the clients, and access control operations
  - Shielded I/O library 
    - SPDK's device driver runs inside the enclave.
  - Trusted counter
    - defer the counter increment until the data is persisted without loosing any availability guarantees. 
  - MemTable
    - devise a mechanism to ensure the confidentiality, integrity, and freshness of the MemTable.
    - partition the existing MemTable in two parts: key path and value path.
      - encrypting the value, enabling fast key lookups inside the enclave.
      - also store the hash of the value inside the enclave.
      - can reduce the space size 
      - ![image-20201110154834949](../paper_figure/image-20201110154834949.png)
  - SSTable
    - maintain the KV pairs persistently
      - group KV pairs together into blocks.
    - encrypt each block, calculate a hash over each block
    - these hashes are then grouped together in a block of hashes and appended at the end of the SSTable file (append at the end of the SSTable file.)
    - all hashes are grouped together in a block of hashes an appended at the end of the SSTable file.

- Operation
  - Put: first encrypts the value of the KV pair and generates a hash over the encrypted data.
    - the encrypted value is copied to the untrusted host memory
    - the hash with a pointer to the value is inserted into the skip list in the enclave.

- Optimizations
  - timer performance: prevent every request from blocking for the trusted counter increment.
  - SPDK performance: add a cache within the enclave.
  - OpenSSL AES-128-GCM: en-/decryption, HMAC
    - 16B wide HMAC.


### Implementation and Evaluation
- Evaluation
  - Compare with unmodified RocksDB
  - stress-test the system by running a client on the same machine as the KV store.

- Performance of the Direct I/O library
  - SPDK v.s. native SODK

- Impact of the EPC paging on MemTable
  - MemTable completely resident in the enclave v.s. split the key and value.

- Throughput and latency measurements 
  - Effect of varying byte sizes
  - Effect of varying workloads
  - Effect of varying threads
  - Latency measurements

- Performance of the Trusted counter
- I/O amplification

## 2. Strength (Contributions of the paper)
1. I/O library for shielded execution (for performance)
a direct I/O library for shielded execution based on Intel SPDK
> perform the I/O operations without existing the secure enclave.

2. Asynchronous trusted monotonic counter 
use the lag in the syn operation in modern KV stores to asynchronously update the counters.
> overcome the limitation of the native SGX counters.

3. Secure LSM data structure
reside outside of the enclave memory while ensuring the integrity, confidentiality and freshness of the data.

4. The whole prototype is built on RocksDB with reasonable overheads.
built on SCONE shielded execution framework
> modified standard C library (SCONE libc)


## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. SPDK
SPDK enables zero-copy I/O by mapping DMA buffers to the user address space. 
> relies on actively polling the device instead of interrupts

2. Do not use Merkle trees 
For its MemTable design, it argues that using Merkle tree can allow the MemTable to be stored outside the EPC memory
> can verify the data integrity by checking the root node hash and each hash down to the leak storing the KV.
> slow lookup: the key has to be decrypted on each traversal.