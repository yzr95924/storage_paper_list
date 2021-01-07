---
typora-copy-images-to: ../paper_figure
---
Understanding Data Deduplication Ratios
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SINA'08 | Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
This paper will explore the significance of deduplication ratios related to specific capacity optimization techniques within the context of information lifecycle management.
>1. cost saving 
>2. risk reduction
>3. process improvement

### Multiple technologies 
- Single instance storage (SIS)
the replacement of duplicate files or objects with references to a shared copy.

- Data deduplication
examining a data set or byte stream at sub-file level and storing and /or sending only unique data.
> the key factor distinguishing data deduplication from SIS is that data is shared at a **sub-file** level.

- Compression
encoding of data to reduce its storage requirement. Lossless data compression methods allow the exact original data to be reconstructed from the compressed data.

- Copy on write and pointer remapping
create changed block point in time copies.

- Thin provisioning 
the transparent allocation of physical storage space for data when it is written ("just in time") rather than in advance of anticipated consumption.



## 4. Future Works
