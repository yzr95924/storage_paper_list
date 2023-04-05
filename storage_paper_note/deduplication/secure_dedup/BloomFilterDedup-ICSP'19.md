---
typora-copy-images-to: ../paper_figure
---
Bloom Filter Based Privacy Preserving Deduplication System
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| Spinger ICSP'19 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Deduplication can be used as a side channel to learn the existence of a particular data in cloud storage .
> the existing solutions delay deduplication process to hide information regarding the presence of data. 
> increase the communication overhead, even if the data is present on storage.

This paper wants to solve this problem via using a bloom filter. 
> a client sends an upload request, the server responds by sending a genuine bloom filter (if data exists) along with some dummy filters.

### Bloom Filter privacy perserving deduplication approach
- In this approach, it uses bloom filter as the identity of the file 
BF is stored at servers as the identity of file, 
> 1. the client who possesses the file, can generate the BF using file blocks and request for access to the file using BF.
> 2. the client sends tag of file, then, the server responds with set of BFs and encryption keys coresponding to the tag.

- Threat Model
1. Semi-trusted Server
Honestly performs the storage operations requested by clients, but is interested in learning plaintext of the outsourced files. (dictionary attacks)
2. Malicious Client
misuse deduplication process to learn the presence of file at storage.


- Security Goal
1. Privacy
An adversary should not be able to learn any information regarding the file.
> the plaintext information, the presence of file.
> Server responds with a set of BFs instead of "Yes/No" as a response.

An adversary cannot learn the existence information without knowledge of the complete file.

2. Integrity
link hash value with stored/sent content.


- Proposed Approach
![1561365990754](../paper_figure/1561365990754.png)

- Eavesdropping
To protect the communication from client to server, it leverages random nonce phenomena ($R$)
> sends $BF \oplus R$ instead of $BF$


### Implementation and Evaluation
- Evaluation
1. Upload time
2. Communication cost


## 2. Strength (Contributions of the paper)
1. This paper uses a bloom filter to achieve privacy preserving deduplication and also provide the security analysis to prove that it can make sense.
> also has the implementation to show the performance.


## 3. Weakness (Limitations of the paper)
1. This method does not totally solve this issue, since if a malicious user owns a file, it can test whether other people own this file as well.
> still exists the information leakage.


## 4. Future Works
1. In this paper, it declears that it only considers the client side, file level, inter user deduplication. (When we consider a problem, we should state the scenario condition clearly.)
>1. client side deduplication or server side deduplication
>2. file level or chunk level
>3. inter user deduplication or intra user deduplication

2. The key idea of this paper is to use the bloom filter as the identity of file. By this way, the server doesn't need to response "Yes/No" directly.