---
typora-copy-images-to: ../paper_figure
---
Length Preserving Compression - Marrying Encryption with Compression
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SYSTOR'21 | Data Compression |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - the integration of  data compression capabilities into many storage systems
    - support random I/O on the compressed data
  - encrypting data at the host, before data is written to the storage, in order to address regulatory and enterprise requirements
    - encrypted data prevents the storage from compressing the data
- This work focuses on data reduction which is deployed on the storage side 
  - **the industry** has widely adopted the practice of relying on storage side data reduction 

### Length preserving compression (LPC)

- Main problem
  - combine `storage-side` data reduction with end-to-end encryption
  - this is done by compressing the data **before** encrypting it
    - the compression may change the length of the data
  - LPC attempts to compress and encrypt a **block** of data 
- Goals:
  - the output is of the same length as the input
  - the output is `nearly` as compressible as the input was
- Write workflow:
  - compress a data sector `at the host`, `encrypt it`, and then `pad the encrypted data block to its original block size` 
    - with a highly compressible padding (e.g., **zeros**)  
  - ![image-20210622023236594](../paper_figure/image-20210622023236594.png)
  - handle incompressible data
    - forgo compression (using a compression threshold)
  - add two bytes of information to the encrypted compressed sector
    - contain the size of the compressed sector
- Read workflow
  - first determine whether it is compressed or not
  - read the two bytes indicating the length of the compressed portion
  - decrypt this portion of the sector to obtain the compressed plaintext data, and decompress the plaintext data
- Discussion
  - **compression block size**: to work with a sector size of 4096 bytes vs. 512 bytes
  - **compression method**: can be replaced it with other compression engines
    - Zlib, Z-standard: which are slower but achieve superior compression ratios
  - **support deduplication**: using convergent encryption but storing the IV along with the encrypted data in the metadata
  - **changing the protocol**?: the entire premise of LPC is to `avoid changes to the protocol between the host and the storage`
- Security analysis
  - information leakage
    - If two sequences of sectors have the same compressibility pattern this increases the probability that those sequences contain `similar data`
  - semantic security within compressibility categories
    - cannot distinguish between two sectors as long as the lengths of their compressed data are same
  - possible security enhancements 
    - hide the exact compressibility of each sector, by adding a few superfluous bytes to the compressed data before encryption
      - the price: a small reduction in compression gains

### Implementation and Evaluation

- Implementation
  - Linux dm-crypt, Xen blktap
  - Encryption: AES-XTS
  - compression: LZ4
- Evaluation
  - trace:
    - random read, random write, sequential read, and sequential write
      - using the VDBench, with different compression ratio
  - Baseline: LPC vs. regular encryption
  - Environment: SSD, iSCSI block device, in-memory volume  
    - only when the storage backend is very fast, as in the case of in-memory storage (tmpfs), do the compression CPU cycles become a bottleneck

## 2. Strength (Contributions of the paper)

- it points out the issue that how to combine data reduction with end-to-end encryption
  - trade-off between security and storage savings

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- Why do not use data reduction (compression) in the host side?
  - it needs a complex data layout management
  - requires a stateful mapping between the application's notion of offset and the actual location of the data
    - compression changes the length of the data
      - this information needs to be updated atomically and persistently along with the compressed data
- End-to-end encryption
  - The trend is driven by regulatory demands
  - the data is never in the clear `except at the host`, yet they eliminate the opportunities for storage side data reduction
- Decompress
  - LZ4 compression engine is much faster in decompression data than compressing it
- Trading security for functionality is a common practice
  - For example, convergent encryption, Order preserving encryption, and CryptoDB
    - all of which pay a price of security flaws for achieving functionalities over encrypted data (`are negated by standard encryption`)
