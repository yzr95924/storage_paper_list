---
typora-copy-images-to: ../paper_figure
---
PRO-ORAM: Practical Read-Only Oblivious RAM
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| RAID'19 | Data Encryption |
[TOC]

## 1. Summary
### Motivation of this paper
Although encryption of data on the cloud guarantees data confidentiality, it is not sufficient to protect user privacy.
> Access patterns on encrypted data leak substantial private information such **secret keys** and **user queries**.

A solution to stop this inference is the use of Oblivious RAM (ORAM)
> continuously shuffle the encrypted data blocks to avoid information leakage via the data access patterns.

A large number of cloud-based storage services have a **read-only model** of data consumption.
> offers only read operations after the initial upload (write) of the content to the cloud.
> Dropbox

- Key Question
whether it is possible to achieve **constant latency** to hide read-only access?

### PRO-ORAM
- Main goals
1. hide read data access patterns on the cloud server.
2. achieve constant time to access each block from the cloud.

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)
1. This paper proposes a practical and secure **read-only** ORAM design for cloud-based data hosting services.
> utilizes sufficient computing units equipped with the SGX.

2. It also provides the **security proof** and efficiency evaluation 
> 


## 3. Weakness (Limitations of the paper)

## 4. Future Works
