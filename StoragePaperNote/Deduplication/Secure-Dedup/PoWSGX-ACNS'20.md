---
typora-copy-images-to: ../paper_figure
---
Proofs of Ownership on Encrypted Cloud Data via Intel SGX
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ACNS'20 | PoW |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation 
  - Traditional PoWs rely on an assumption that the cloud server is **fully** trusted and has access to the original file content.
    - In practice, the cloud server is not fully trusted 
    - the data owners may store their encrypted data in the cloud 
  - hindering execution of the traditional PoWs.

- Main idea
  - create a trusted execution environment in a cloud server (Intel SGX)
  - the critical component of the PoW verification will be executed in this secure environment


### PoWIS
- System model
  - file-based client-side deduplication
  - Two entities: the cloud server and the data owner 

- Theat model
  - the cloud server is honest-but-curious
    - tries to learn sensitive information from the encrypted file 
  - the malicious data owner wants to pass the PoW check on a file without actually possessing this file
- communication channel is protected by SSL/TLS.
  
- Main design
  - The PoW verification process is separated and delegated to the SGX enclave.
  - The decryption key for decrypting the encrypted cloud data and the PoW proof will be transmitted via a secure channel.
    - remain confidential to the untrusted cloud server.
    - the stored encrypted cloud data (will be decrypted in the secure enclave via the decryption key sent by the client)
  - The enclave uses the session key $K$ to perform decryption to obtain $K_{mle}$ and the PoW proof.


- Remote attestation
  - allow the client to attest the enclave and to negotiate a *session key* to protect communication between the client and the enclave.
  - The session key $K=g^{ab}$, $g^a$ is the public key share from the enclave, and $g^b$ is the public key share from the client.

- Security analysis
  - It simply uses SGX as a *black box*, which is assumed to be secure.
  - A malicious client cannot pass the PoW check without possessing the original file.
  - The cloud server cannot learn anything about the original file.
### Implementation and Evaluation

- Implementation
  - sgx-ssl, sgx-sdk
  - C language

- Performance 
  - RA breakdown
  - PoW proof generation and verification time


## 2. Strength (Contributions of the paper)
1. PoWIS is the first secure PoW protocol designed for encrypted cloud data.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. an immediate remediation is to ask potential data owner to first encrypt the original file, and then compute the PoW proof over the encrypted file.
> this can only ensure that prover really owns the encrypted file, instead of the original file.

2. Original PoW: Merkle tree is first constructed over a file, and resulting Merkle root will be stored by the cloud server.
> the cloud server will issue a challenge to the client.

3. Remote Attestation
Via the RA, the client can ensure that the enclave is running on the remote cloud server and executions inside the enclave are trustworthy.
> a secure channel can be established between the client and the enclave at the same time. (allow the client to communicate with the enclave directly)


4. Performance consideration 
By randomly checking a certain number of file blocks (e.g., 460), the cloud server can detect this misbehavior with a high probability (e.g., 99%)
