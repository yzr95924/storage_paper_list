---
typora-copy-images-to: ../paper_figure
---
ShieldStore: Shielded In-memory Key-Value Storage with SGX
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| EuroSys'19 | SGX |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
  - the memory requirements of in-memory key-value store are far larger than the protected memory limit.
  - main data structures (e.g., pointers and keys) do not match well with the coarse-grained paging of the SGX memory extension technique. 
  - SGX enables cloud users to run their applications **securely** on the *remote* cloud servers whose operating systems and hardware are exposed to potentially malicious remote attackers and staff. 

This paper wants to overcome the memory restriction.
> maintain the main data structures in unprotected memory with *each key-value pair individually encrypted*
> integrity-protected by its secure component running inside an enclave.

### ShieldStore
- SGX basis
  - one major limitation of SGX: the capacity of the protected memory region, enclave page cache (EPC). (128MB)
  - If enclave memory usage is larger than the EPC limit, some pages are *evicted* from EPC, and remapped to the *non-EPC* memory region with **page granularity** encryption and integrity protection. 
  - Performance penalty: demand paging step which maps the page back to the secure region along with the eviction of another victim page.
  - The data in EPC are in plaintext only in on-chip caches, and they are encrypted and integrity-protected when are in the external memory.
  - Creating a huge Merkle tree for tens or hundreds of gigabytes of main memory at cacheline granularity will increase the integrity verification latency intolerably.
  - the effective EPC is smaller than the 128MB reserved region (in practice around 90MB) due to **security meta-data**.
  - Cost of crossing enclave boundary
    - an enclave cannot issue a system call (any system services require exiting the enclave), needs hardware operations such as *flushing TLB entires*. 	

- Main idea
  - Instead of relying on the page-oriented enclave memory extension provided by SGX, it designs that fine-grained application-specific data protection. (maintains the majority of data structures in the non-enclave memory region)
  - Inside the enclave: encrypts each key-value pair.
  - Non-enclave memory: only place the encrypted and integrity-protected data.

- Architecture

![image-20200714012030378](../paper_figure/image-20200714012030378.png)

1. The client remote-attests the storage server system

verifying the SGX support of the processor, the code, and other critical memory state of an enclave.

2. The client and storage server running in the enclave generate session keys an exchange them.

3. The client sends operations through the secure channel using the exchanged session key.

Clients do not directly access the ciphertexts on the server side
> the server in the enclave will decrypt  the retrieved data
> encrypt them again with the session key used for the client, send the response to the client


- The performance degradation of accessing enclave memory pages when the enclave memory size exceeds the EPC limit. (read and write)
  - Three types
    - No SGX: SGX disable
    - SGX enclave: the entire data resides in the enclave
    - SGX unprotected: access instructions are running inside the enclave, the data is in the unprotected memory region
    - reading or writing unprotected data from an enclave shows similar latencies to No SGX

![image-20200714142649199](../paper_figure/image-20200714142649199.png)

- Baseline Approach

Employ a hash-based index structure, and use chaining to resolve collisions in hash-based index.
> store the entire hash table in the enclave memory. 
> As the EPC region can cover only a small portion of the total database size, a data access can cause page eviction and demand paging between an EPC page and non-EPC page.

- SheildStore Design
  - Overall architecture:
    - the main hash table storing key and data pair is placed in the **unprotected memory region**. Each data entry must be encrypted by ShieldStore in an enclave.
    - the encrypted data entry is retrieved from the main hash table in unprotected memory region. (decryption and integrity verification MAC) 
  - Fine-grained Key-value encryption
    - MAC Hashing: attaches a 128-bit hash value created from *encrypted key/value, key/value sizes, key-index, and IV/counter,* to the data entry. 
  - Integrity verification
    - create many in-enclave hashes
    - Each MAC hash value covers one or more buckets
  - Persistency Support (seal secure meta-data in the enclave)
    - fork a child process stores the key-value entries on a file. (initiated every 60 seconds)
    - write the encrypted key-value entries in unprotected memory are directly written to storage.

- Optimizations
  - Extra Heap Allocator
    - Each call to the outside heap allocator requires a costly exit from the enclave.
    - Add custom allocator which runs inside the enclave, but allocates unprotected memory.    
  - MAC bucketing
    - reduce the overhead of the MAC accesses through pointer chasing, each hash chain has its own MAC bucket (contains only the MAC fields of data entries of the hash bucket).
  - Multi-threading
    - assign each thread to an exclusive partition of hash keys
  - Searching encrypted key 
    - use a small 1 byte key hint in the data entry
    - the key hint is a hash of the plaintext key stored in each data entry (leakage of 1 byte if hashed key information)


### Implementation and Evaluation
- Workloads
  - uniform workload: generate key with uniform distribution
  - zipf distribution of skewness 0.99 (used in YCSB)

- Evaluation
  - Baseline: put the entire hash table in enclave memory  
  - GrapheneSGX: Memcached + graphene
  - ShieldBase: without optimizations
  - ShieldOpt: final version

1. Standalone evaluation
Overall Performance
Multi-core Scalability
Eﬀect of Optimizations
Trade-oﬀs in MAC hashes

2. Comparison to Eleos

3. Networked Evaluation
a new extra overhead is added to ShieldStore
> the cost of receiving requests and sending responses through the socket interfaces
> using HotCalls


## 2. Strength (Contributions of the paper)
- Good experiments

## 3. Weakness (Limitations of the paper)
- SGX does not provide direct protection for such side channels
- This paper also assumes communication between clients and ShieldStore is protected through encryption.
- The hash-based index structure of ShieldStore does not support range query operations.

## 4. Some Insights (Future work)
- Reading unprotected memory from an enclave is as fast as no-SGX reads.