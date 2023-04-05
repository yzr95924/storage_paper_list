---
typora-copy-images-to: ../paper_figure
---
When Delta Sync Meets Message-Locked Encryption: a Feature-based Data Sync Scheme for Encrypted Cloud Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ICDCS'21 | Delta Sync + Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - delta sync achieves efficient cloud sync by **synchronizing only the updated part** of the file
  - the huge data in the cloud need to be deduplicated and encrypted (e.g., message-locked encryption)
    - deduplication between different users
  - when both are combined, few updates in the content can cause **large sync traffic amplification**
    - for both keys and ciphertext in the MLE-based cloud storage
    - once a plaintext chunk is updated, MLE will generate a new key of the updated plaintext chunk and hence produce the new ciphertext chunk 
      - is entirely different from the old one (several bytes --> the whole ciphertext chunk)
      - ![image-20210928000837995](../paper_figure/image-20210928000837995.png)
  - the **first work** that pays close attention to the delta sync problem for encrypted cloud storage service.
    - *alleviate the sync traffic for both the ciphertext chunks and keys, while still enabling deduplication on ciphertext*

### FeatureSync

- System model
  - Data sync + MLE-based cloud storage
    - the client and the server need to maintain the hash values of the chunks for each file
      - the **changed chunk** will be uploaded to the cloud to replace the old one (client-side deduplication??)
    - the cloud storage uses MLE 
- Observations and objectives
  - sync traffic of ciphertext, sync traffic of keys, and the traffic amplification
  - how the sync traffic is affected by the data insertion
  - The sync traffic of ciphertext and keys both trend increasing with the size of the dataset
    - also increase with the number of insertions
- Feature-based encryption
  - select **a representative hash value** of the chunks within the same file
    - When a small amount of changes are introduced into the data, the feature value of the chunk will not change
    - using Sketch (FAST'12) to find similar chunks, if two chunks have the same sketch, they may be close to repetition
  - maintain a key for each file
- Merge and send procedure
  - RTT
    - delta sync: needs four round trips
    - full sync: needs two round trips
  - add multiple ciphertexts into a single package, and finally sens the package
- Dynamic fine-grained sliding window for delta sync
  - The smaller the sliding window, the more redundancy will be found
    - increase the number of hash values sent by the server, and the hash computation cost within the client
  - need to dynamically adjust the size of the sliding window according to the size attribute of the files
    - When a large number of small files are merged into a large file, the increments in the file will be small and discrete

### Implementation and Evaluation

- Implementation
  - C, adding two main modules within **rsync**
- Evaluation
  - Dataset:
    - Two versions: for linux, Tensorflow, Opencv
  - Baseline:
    - fixed-block MLE + Rsync
    - CDC MLE + Rsync
    - CDC MLE + PandaSync
  - Sync time
  - Network traffic
  - Breakdown of the sync
    - encryption and merging files
  - Number of keys

## 2. Strength (Contributions of the paper)

- a new problem for combining sync + MLE

## 3. Weakness (Limitations of the paper)

- bad presentation

## 4. Some Insights (Future work)

- Full sync vs. delta sync
  - full sync: directly uploads the entire file after modification to replace the old version of the file
  - delta sync: transmits the delta (i.e., *the difference between the old and new versions of the file*) to the cloud.
