---
typora-copy-images-to: ../paper_figure
---
The Overhead of Confidentiality and Client-side Encryption in Cloud Storage Systems
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| UCC'19 | Data Encryption |
[TOC]

## 1. Summary
### Motivation of this paper

Many cloud storage services are fairly blunt regarding the lack of confidentiality they provide.
> a solution to provide confidential cloud storage is to use client-side encryption (CSE).

- The issue of CSE
CSE complicates file synchronization techniques, such as deduplication and delta encoding, commonly used to reduce the traffic overheads associated with personal cloud storage systems.

- The goal of this paper
this paper presents empirical experiments and analysis of CSE-related overheads.
> compare and contrast the security and bandwidth saving features implemented by CSE services and non-CSE services.
> compression, delta encoding, and deduplication

### Data confidentiality overhead
This paper focuses on pure CSEs.

- Service
1. CSEs
Mega, Sync.com, SpiderOak, Tresorit
> Mega: AES-128
> SpiderOak: AES-256-CFB 
> Password-Based Key Derivation Function 2 (PBKDF2)

2. non-CSEs
Dropbox, iCloud, Google Drive, Microsoft OneDrive 
> This was selected base on recommendations in online reviews
> https://www.cloudwards.net/comparison/

- Baseline methodology
adding files to the cloud services' sync folders and performing targeted system and network measurements during the sync process.

1. Network traffic
using Python modules **netifaces** and **pcapy** among others.
2. CPU memory overhead
using Python modules **psutil** 


- Bandwidth saving feature
![1569071462061](../paper_figure/1569071462061.png)
1. Client-side deduplication
Dropbox, iCloud, Mega, SpiderOak, and Sync.com
2. Other
Google drive, One Drive and Tresorit

![1569076126086](../paper_figure/1569076126086.png)

### Implementation and Evaluation

- Evaluation
Setting: Macbook Air, high-speed university network through 10Gb/s.

## 2. Strength (Contributions of the paper)

1. This paper presents a comprehensive analysis of state-of-art cloud storage services

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. how to combine delta encoding with CSEs
the development of optimized delta encoding policies for CSEs, which minimize the bandwidth and storage overhead associated with CSE.