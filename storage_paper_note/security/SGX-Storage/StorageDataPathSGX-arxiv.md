---
typora-copy-images-to: ../paper_figure
---
Securing the Storage Data Path with SGX Enclaves
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| arxiv | Storage SGX |
[TOC]

## 1. Summary
### Motivation of this paper
This paper intends to explore the use of SGX enclaves as a mean to improve the security of handling keys and data in storage systems.

- Motivation 
this paper studies the performance of securing the data path and hardening the security of data-at-rest encryption in storage systems.
> such as block storage systems, file systems or object storage.

1. high throughput, comparable to running the same operation without enclaves.
2. the subtleties arise from using SGX
> how much development work is required in order to get acceptable 
performance?

3. quantify the performance effect of the storage encryption use-case, and understand the subtleties involved that may affect a wide array of other use cases.

### Method Name
- SGX performance overhead
1. the actual overhead of accessing the encrypted memory via the MEE.
2. the overhead associated with entering and exiting an enclave.

> ECALLs (defined in an "edl" file) has a performance impact due to the CPU's context switches.
> the limitation of the EPC size, and when operating with memory that exceeds the EPC size there is a need for paging of EPC pages to regular memory. (additional latency, encryption  of this data before it lands in regular memory)

- Data-at-rest encryption
either clear text or encrypted in transit (e.g., HTTPS), and then encrypted before being persisted to disk.
> ensure that all persistent data is always encrypted, so that loss of hardware (e.g., malicious user, hardware failure), does not compromise the data.

- Software encryption
the encryption keys must reside in clear text in memory, presenting a significant security risk.
> sensitive data keys are vulnerable to either privileged users or to memory sniffing techniques.


- Protecting the keys using SGX
By placing the software encryption process and key handling inside enclaves.
> perform all encryption and decryption in SGX

Enclaves are allowed to access the general memory, and so data buffers (both encrypted and cleartext) can reside outside of the enclaves memory.
> call ECALL, gets pointers to two buffers in the general (non-encrypted) memory.

![image-20191217205029441](../paper_figure/image-20191217205029441.png)

- End-to-end Data protection
Data-in-transit encryption is now required in many scenarios.
> via one of the prevalent standards such asa HTTPS, SSL, TLS.

![image-20191217204932349](../paper_figure/image-20191217204932349.png)

### Implementation and Evaluation
- Evaluation
1. implement four variations of the function **find_max**.
> ECALL, in, user_check
> in: copy the data to enclave.
> user_check: without copying it into the enclave's memory.

2. data encryption (AES128-GCM)
> trusted: sgxsdk, sgxssl 
> untrusted: openssl

result: openssl > sgxssl > sgxsdk

3. The effect of multiple threads
> Multiple processes
> Multiple threads with a single enclave
> Multiple threads with separate enclaves



## 2. Strength (Contributions of the paper)
1. the most important thing mentioned by this paper is *without actual performance testing*, it is difficult to predict the performance of computations running inside an SGX enclave.
> choosing buffer sizes, library configuration

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. In this paper, it mentions the **original concept** of SGX. 
created with less ambitious use cases in mind, in which minimal parts of the computation (and the code associated with it) are placed into enclaves.
> only a saml part of the application needs to reside in an enclave.
> data transformations, local test on data, and cryptographic functions on data blocks. 

2. It also mentions some workloads are bad fro SGX performance.
> require frequent small random access operations
> require a very large amount of data to be in encrypted memory simultaneously

3. SGX basis
**Memory encryption engine (MEE)**: perform real time encryption of all communication between the CPU and the memory.
> MEE is only invoked on a special designated area of the memory called the **Enclave Page Cache (EPC)**. (128MB: 96MB after reducing space used for managing the enclaves). 
