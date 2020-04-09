---
typora-copy-images-to: ../paper_figure
---
Mitigating Traffic-based Side Channel Attacks in Bandwidth-efficient Cloud Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| IPDPS'18 | Deduplication Side Channel Attack |
[TOC]

## 1. Summary
### Motivation of this paper
The occurrence of deduplication can be easily identified by monitoring and analyzing network traffic, which leads to the risk of user privacy leakage.
> Learning-the-remaining-information (LRI) attack.

Existing work addresses the LRI attack ath the cost of the high bandwidth consumption.

This paper proposes **randomized redundant chunk scheme** (RRCS) to mitigate the risk of the LRI attack while maintaing the high bandwidth efficiency of deduplication.
> add randomized redundant chunks to mix up the real deduplication states of files used for the LRI attack.
> obfuscates the view of the attacker.

This paper argues current cloud storage system typically perform **cross-user source-based** deduplication
> for higher storage and bandwidth efficiency.

### Randomized redundant chunk scheme
- This paper focuses on the side channel of traffic information. (like existing work.) 
> the attacker could only infer/probe/ the privacy by observing the amount of the network traffic between the client and server.

- LRI attack
The goal of the adversary is to get the sensitive information
> can be represented as a small number of bits and easily covered in one-chunk size(about) in the chunk level.
> the number of possible versions of the target file is moderate 

- LRI attack in chunk-level deduplication
> Three deduplication states for a file: 
> 1. full deduplication: do not upload any chunk
> 2. partial deduplication: upload some chunks 
> 3. no deduplication: full upload

The key idea of this paper is to leverage chunk-level redundancy rather than the whole-file redundancy to mix up the deduplication states of the target file state
> via uploading som redundant chunks in each file.

- Deterministic version
1. For the file without non-duplicate chunks, i.e., the whole file is duplicate, it randomly choose one chunk of the file to upload.
> is easily broken, can append one non-duplicate chunk in each file to broken the solution

2. For the file with non-duplicate chunks, it directly uploads its non-duplicate chunks

However, using deterministic chunk-level redundancy for defending against LRI attack may be easily broken via Appending Chunks attack (ACA)
> may fail to mitigate the risk of LRI.


- Randomized Redundant Chunk Scheme
The basic idea of adding redundant chunks is to choose the number of the redundant chunks from a range uniformly at random.
> weaken the correlation between the duplicated and the existing files in the server from the network traffic point of view.
> the number of redundant chunks is chosen uniformly at random, $N$ is the total number of chunks in a file. $[0, \lambda N]$, $\lambda​$ is a tradoff between the security and bandwidth efficiency.
> provide the security analysis for the range, and prove there still exists the risk of privacy leakage.

- Overview workflow
RRCS is implemented in the server.
1. the client first divides the file into chunks using chunking algorithm
2. the client upload the fingerprints of all chunks to the server 
3. after receiving the fingerprints, the server can know the deduplication state of the file via querying the fingerprint index.
4. the server randomly chooses $R$ duplicate chunks and non-duplicate chunks, and responds to the client. 


### Implementation and Evaluation

- Evaluation
1. Datasets
> FSLhome, MaxOS, Onefull (ATC'11): not public
> This paper argues that since the files havin few copies account fo a significant proportion, RTS of Danny Harnik becomes bandwidth-inefficient in the real-world datasets

2. Bandwidth overhead
Total traffic with the increase of the number of file uploads, compare with 
> 1. Target-based deduplication
> 2. Source-based deduplication
> 3. File-level RTS
> 4. Chunk-level RTS
> 5. RRCS


## 2. Strength (Contributions of the paper)
1. this paper proposes **randomized redundant chunk scheme (RRCS)** to mitigate the risk of the LRI attack in cloud storage, while maintaining the high efficiency of deduplication.

2. It gives an in-depth security analysis for RRCS
> all possible variants of the deduplication detection method are not effective in RRCS.

3. Implement a prototype in deduplication system, and examine the real performance of RRCS by using multiple large-scale real-world datasets.


## 3. Weakness (Limitations of the paper)
1. This paper has a very strong assumption that the senstive information should be included in just one chunk. This is a little bit weak.


## 4. Future Works
1. This paper argues the LRI attack in deduplication is difficult to be addressed due to the following challenges:
> CE or MLE has limitations: the attack could always carry out the LRI attack based on the side channel of network traffic to preceive whether deduplication occurs without probing the data themselves transmitted in the network.


2. Current side channel attack defence is ineffiecent:
> disable cross-user deduplication via encryption. (personal encryption key)
> just perform target-based deduplication 

3. This paper argues that Harnik's work 
> cause huge bandwidth overhead due to upload redundant data.
> has the risk of leaking privacy with a certain probability.


