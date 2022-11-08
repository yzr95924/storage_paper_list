---
typora-copy-images-to: ../paper_figure
---
# Distributed Key Generation for Encrypted Deduplication: Achieving the Strongest Privacy

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CCSW'14 | Encrypted Deduplication |
[TOC]

## 1. Summary

### Motivation of this paper
1. Provide a new security notion
For server-aided MLE
> show it is strictly stronger than all relevant notions. Lacking in original paper 

2. Introduce a distributed protocol 
eliminates the need of the key server 
> allows less managed system such as **P2P** systems to enjoy the high security level.

- The limitations of DupLESS
relaying on a dedicated key server
> difficult to deploy in a less managed setting such as P2P systems
> impairs its security, as compromising a single key server reduces its protection to that of CE.

### Distributed Key Generation

- Security notion
  - A new security notion of the encrypted deduplication: D-IND$CPA
    - ciphertext indistinguishability (IND) 
    - the adversary is restricted to querying the encryption oracle with *only distinct messages*
  - MLE or D-PKE cannot be semantically secure, as they leak **message equality**. 

- Eliminating the key server

1. A distributed protocol removes the need for a centralized key server.
> for P2P paradigm, it attains the same security as DupLESS

2. As long as the user obtains the cooperation of any qualified subset of *players*, it can perform the desired operation.

3. Threshold signature
use Shoup's RSA-based scheme
> variant of RSA threshold signature scheme

Distributed oblivious key generation (DOPG)
> signature shares 
> proof of correctness
> combining shares
> blinding: blind signature shares


### Implementation and Evaluation
- Implementation
  - Shoup's threshold signature with a number of optimizations to improve its performance 
  - in Java

- Evaluation
  - Microbenchmarks
  - Computation time
    - the client latency for the entire key generation process, including both computation
      - signer's signing time and client's combining time
      - network latency 
  - Impact on upload throughput  

## 2. Strength (Contributions of the paper)
1. propose a new security notion

DupLESS lacks a rigorous security notion to verify its security 

2. a distributed key generation scheme 
For P2P system
Based on threshold signature

## 3. Weakness (Limitations of the paper)
1. the performance overhead of Shoup's RSA signature scheme is still high.


## 4. Some Insights (Future work)
1. The weakness of convergent encryption
lacking a rigorous theoretical treatment.
> Server-aided MLE provides the best possible security for encrypted deduplication.

2. P2P system
the data can be scattered in a P2P fashion among the users.
> without a storage service provider.

3. semantic security 
if any probabilistic, polynomial-time adversary that is given the ciphertext of a certain message, and the message's length, cannot determine any partial information on the message.

4. attack type in secure definition
> CPA: chosen plaintext attacks
> CCA1: chosen ciphertext attack
> CCA2: adaptive chosen ciphertext attack