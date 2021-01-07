---
typora-copy-images-to: ../paper_figure
---
SeGShare: Secure Group File Sharing in the Cloud using Enclaves
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| DSN'20 | SGX Storage |
[TOC]

## 1. Summary

### Motivation of this paper

- Present a new architecture for end-to-end encrypted, group-based file sharing using Intel SGX
  - protect the confidentiality and integrity of all data and management files 
  - overhead is extremely small in computation and storage resources.

- the drawbacks of current solutions
  - For permission revocation: it is necessary to **re-encrypt** the file with a new key and distribute the new key to many users (*involves expensive cryptographic operations*)
    - the users can gain plaintext access to the **file key**.  

- Key reason for throughput improvement
  - does not require complex cryptographic operations on permission or membership changes.
  - build an efficient SGX-enable TLS stack, use *switchless* enclave calls for all network and file traffic.


### File Sharing System

- Attacker Model
  - CA is trusted: an attacker controlling multiple users should only have permissions according to the union of permissions of the individual controlled users.
  - Malicious cloud provider: monitor/change data on disk or in memory, view all network communications
  - do not protect the number of files, the file sizes, and the file access pattern 
- Group-based permission
  - a user is a member of a group, and a group has some permissions for files 
  - main benefit: a membership update is sufficient to provide or revoke a user's access to many files instead of changing the permissions of all affected files individually.

- System Design

![image-20200731155149662](../paper_figure/image-20200731155149662.png)

1. Setup: establish trust between users and enclave
Establishes bilateral trust between each user $u \in U$ and the enclave running at the cloud provider.
> Establish user trust in enclave
> Establish enclave trust in users

2. Access control
This component is responsible to relation update and access control check
> It uses the file manager components to *read* and *write* the required relations.
> It is the key to enable dynamic groups without **re-encryption**.

3. File managers
It contains two parts: 
> **trusted file manager**: encrypt/decrypt the content of all files that should be written/read with *PAE_Enc/PAE_Dec* using a unique file key $SK_f$ per file. ($SK_f$ is derived from a root key $SK_r$) 
> **untrusted file manager**: passed/received all encrypted data, and handle the actual memory access.

*Content store*: regular files and its corresponding ACL files 
*Group store*: group list files and member list files

- Immediate revocation 
  - membership updates are enforced instantly without time-consuming **re-encryption** of files.
  - a permission update only requires 
    - one decryption of the corresponding ACL file.
    - one insert or update operation.
    - one encryption of the ACL.

- Probabilistic authenticated encryption (PAE)
Provide the confidentiality and integrity of content files, permissions, existing groups, and group memberships


- Extensions
  - Deduplication
    - Introducing a third store
    - For each uploaded content file: calculate an HMAC over the file's content using the root key $SK_r$ (as the fingerprint)
    - **plaintext data is deduplicated and only a single copy is encrypted** 
      - the enclave has access to the file key
  - Rollback protection for individual files/whole file system
    - Use a Merkle hash tree
    - Use multiset hashes to improve performance 
    - use TEE's monotonic counter to record the version   

### Implementation and Evaluation
- Implementation
  - C/C++ using the Intel SGX SDK

- Evaluation
  - 1. latency to upload/download
  - 2. latency to add/revoke a user to/from his group
  - 3. latency to add/revoke a group permission
  - 4. latency to individual file rollback protection extension
  - 5. storage overhead  

## 2. Strength (Contributions of the paper)
1. New architecture of end-to-end encrypted group file sharing system using a *server-side* Intel SGX.
2. Support data deduplication, rollback protection, and separation of authentication and authorization.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. The drawback of client-side enclave
the heterogeneity of end-user devices

2. the point of implementing with SGX
Given the memory and computational limitations of SGX enclaves (e.g., trusted computing base (TCB) size, trusted/untrusted transition latency), it is *far from trivial* to develop such a proxy service able to scale and sustain a high data throughput, considering dynamic access control operations.

3. About Intel SGX background
Intel SGX is an instruction set
> 1. Memory isolation
> 2. Attestation: allows to establish a secure channel between an external party and an enclave
>
> > this secure channel can be used to deploy sensitive data (e.g., encryption keys) directly into the enclave. 
>
> 3. Data sealing
> 4. Protected file system library : a library shipped with Intel SGX SDK, provides a subset of the file API, e.g., file creation, file writing, and file reading.
> 5. Switchless calls: In SGX's SDK, calls into the enclave are replaced by writing tasks into an untrusted buffer and enclave worker threads asynchronously perform the task.


4. About the category of file sharing systems
> 1. Pure cryptographically protected file sharing systems
> 2. TEE-supported file sharing systems: NEXUS, Pesos