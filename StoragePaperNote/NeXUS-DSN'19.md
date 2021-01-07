---
typora-copy-images-to: ../paper_figure
---
NEXUS: Practical and Secure Access Control on Untrusted Storage Platforms using Client-side SGX
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| UCC'19 | Data Encryption |
[TOC]

## 1. Summary

### Motivation of this paper
- Motivation
  - File sharing: allow user to store, share and synchronize files online.
  - Secure issues:
    - frequent data breaches 
    - data confidentiality and integrity
  - **Purely cryptographic approaches** incur very high overheads on user revocation.
    - when decrypting files on a client machine, the encryption key is inevitably exposed to the client application and can be cached by the user. (need *re-encryption*)   	 
  - Existing approaches
    - require server-side hardware support (limits their availability for users of personal cloud storage services)
      - should not rely on the service provider


### NEXUS
- Main Idea
  - Leverage Intel SGX to provide efficient access control and policy management
  - client-side cryptographic operations implemented inside an SGX enclave.
    - embeds user-specified access control policies into *files'  cryptographically protected metadata*. (enforced by the enclave, smaller attached metadata)

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

