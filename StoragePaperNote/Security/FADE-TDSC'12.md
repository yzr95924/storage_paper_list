---
typora-copy-images-to: ../paper_figure
---
Secure Overlay Cloud Storage with Access Control and Assured Deletion
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| TDSC'12 | assured deletion |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
1. **Access control**: ensure only authorized parties can access the outsourced data on the cloud.
2. **Assured deletion**: outsourced data is permanently inaccessible to anybody (including the data owner) upon requests of deletion of data.
> achieving assured deletion is that it has to trust cloud storage providers to actually delete data, but they may be reluctant in doing so.


Need to build a system that can enforce access control and assured deletion of outsourced data on the cloud in a *fine-grained manner*.

### FADE

- General policy-based deletion
generalize time-based deletion to policy-based deletion
> associate each file with a single atomic file access policy (**Boolean combinatrion of atomic policies**)
> each policy has a control key

- FADE overview
1. Key 
> Data key: for AES encryption for data content
> Control key: a public-private key pair, use to *encrypt/decrypt* the data keys (maintained by the quorum of key managers)
> Access key: the private access is maintained by the FADE client (ABE encryption)

2. Security goals (for active files and deleted files)
> policy-based access control
> policy-based assured deletion

3. Entities
FADE client, Key managers, cloud provider (thin-cloud interfaces)


- Extensions of FADE 
1. Add the access control with ABE
Using **attribute-based encryption (ABE)** to achieve access control
> the client needs to satisfy the policy combination
> the key manager leverages the **public access key** to encrypt the response messages returned to the clients

In this extended version, FADE now uses two independent keys for each policy. (access control and assured deletion)

2. Multiple Key Managers 
To avoid the single-point-of-failure problem, it uses Shamir's scheme
> M < N 
> when deletion, it needs to remove (N-M+1) private control keys corresponding to the policy.


- Security analysis
1. Active files:
An active file is encrypted with a data key (can only be decrypted by key manager).
The response from key manager is protected by ABE-base access key.

2. Deleted files:
The key manager has purged the *control key* for the revoked policy permanently, the adversary loses the ability to decrypt the data key.


### Implementation and Evaluation
- Implementation
1. using OpenSSL for cryptograpghic operations
2. using *cpabe* library for the ABE-based access control
3. *ssss* library for secret sharing
4. *LibAWS++* for interfacing with Amazon S3 uisng plain HTTP
5. Each file has a own metadata 
> contains the specification of the Boolean combination of policies.
> the corresponding cryptographic keys (encrypted data key).
> the control keys associated with the policies.

- Evaluation (performance overhead and monetary overhead of FADE)
1. file transmission time, metadata transmission time, cryptographic operation time


## 2. Strength (Contributions of the paper)
1. propose a general policy-based file assured deletion scheme, and fault-tolerant key management 
2. implement the prototype over Amazon S3

## 3. Weakness (Limitations of the paper)
1. it is not clear how to implement policy in the prototype implementation

## 4. Some Insights (Future work)
1. In this paper, it introduces the idea of assured deletion which is using a control key to encrypt data key
> this idea can be used in other scenario when we consider assured deletion.
