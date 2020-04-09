---
typora-copy-images-to: ../paper_figure
---
RARE: Defeating Side Channels based on Data-Deduplication in Cloud Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| INFOCOM'18 | Deduplication Side Channel |
[TOC]

## 1. Summary
### Motivation of this paper
The commercial cloud storage services are in favor of the **cross-user client-side fixed-size-chunk-level** data deduplication to reach the highest deduplication gain.
> the user uploads the file hash as the duplication check request
> the cloud may check the status of file existence and then sends a binary duplication check reponse (dc response) to the user (suppress the explicit uploading of the file when the deduplication occurs)

The threat from side-channel 
> the user needs a **deterministic response** from the cloud to know whether the further uploading of the file is necessary.

- Key point
Any deterministic response can be seen as an indicator of privacy leakage.


### RARE
- Key point
the privacy leakage of side channel is due to the **deterministic relation** between duplication check request (dc request), duplication check response (dc response)
> this work intends to reach the probabilistic relation by allowing the cloud to randomize the dc response.
> keep the deduplication gain to the certain degree and eliminate the leakage of chunk existence status.

- Main idea
1. duplicate check of single chunk does not give sufficient room for dc response randomization, this paper performs the duplicate check on two chunks at once
2. dirty chunks
the chunks have been queried but not uploaded eventually can be exploited to perform repeated duplicate checks


- Privacy notion
1. existence privacy and inexistence privacy 
> dc response does not give any extra information about the existence status of a determined chunk.

2. weaker version of existence privacy 
$P[C|f(c,aux)] = P[C] = 1/2$


- Check double chunks 
In order to hide the chunk existence status, RARE carries out the encodings on both the dc response and the chunks to be uploaded.
![1568705054880](../paper_figure/1568705054880.png)


- Dirty chunk list
RARE prevents the case that the attacker performs duplicate check but does not upload queried chunks
> it implements a dirty chunk list to keep all hashes of chunks that have been queried but are not uploaded eventually.


- Security analysis
This work aims to achieve the inexistence privacy and weak existence privacy.

### Implementation and Evaluation
- Evaluation
1. Dataset 
Enrom Email dataset

2. communication cost
the number of bits required during the entire chunk uploading process
> duplicate check (dc response) and explicit chunk uploading (chunk)


## 2. Strength (Contributions of the paper)
1. Parameterless configuration 
RARE does not have the parameters that need to be determined manually.
> relieve the burden for engineers

2. No independent server
RARE only involves the interactions between the user and cloud.


## 3. Weakness (Limitations of the paper)
1. the use of dirty chunks actually compromises the deduplication benefit. All of the dc requests relevant to dirty chunks will not trigger deduplication.


## 4. Future Works
1. the random response scheme is a typical method to defend the side channel attack in client side deduplication.
> how to design a tunable random response scheme to balance the overhead of given up storage efficiency and security gain.