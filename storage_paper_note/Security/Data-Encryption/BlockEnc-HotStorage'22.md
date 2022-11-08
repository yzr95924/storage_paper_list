---
typora-copy-images-to: ../paper_figure
---
# Rethinking Block Storage Encryption with Virtual Disks

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'22 | Data Encryption |
[TOC]

## 1. Summary
### Motivation of this paper

- disk encryption
  - length preserving and **do not require storing any additional information** with an encrypted disk sector
    - no room left to store the IV alongside the encrypted sectors
    - no room to store a MAC associated with each sector
  - force the encryption to be **deterministic** and disallow integrity mechanisms
    - lower security guarantees
- motivation
  - disk encryption in a virtual disk that supports versioning and snapshots
    - amplified the shortcoming in disk encryption
  - standard practice forfeits some security for ease of management and performance considerations
- main problem
  - explore how best to implement **additional per-sector information** in Ceph RBD
    - client-side encryption

### Per-sector information in disk encryption

- shortcoming in AES-XTS
  - changing a single bit in the sector (without changing the key or IV) --> yield the expected change only to the **sub-block** in the cipher to which this bit belongs
    - during an overwrite, an adversary can detect exactly **which of the sub-blocks** has changed and which have remained the same
    - leak some information about the relation between the plaintexts at a sector granularity
    - why AES-XTS is widely used for disk encryption? due to practicality
- Ceph RBD
  - distributes each I/O to its corresponding OSD via RADOS
    - support transactions in which writes of several small I/Os are guaranteed to be written **atomically**
    - LUKS standard: AES-XTS (client-side encryption)
- design choices
  - ![image-20220924003433635](./../paper_figure/image-20220924003433635.png)
  - a): each access is contiguous to the data and its matching IV
  - b): store all the IVs of the object (4 MiB) after encrypted data
  - c): a key-value database (RocksDB) to store the IVs
    - key is the offset of the block in the object
    - supports accessing multiple values based on a range of integer keys with a single operation

### Implementation and Evaluation

- implementation
  - modify the built-in client-side encryption in Ceph RBD
    - use a fresh random IV per each sector write
    - IV is persisted to disk to be used during read operations
- evaluation
  - baseline: LUKS2 in Ceph RBD, deterministic LBA based IVs
  - vary the size of I/Os
    - b) is better

## 2. Strength (Contributions of the paper)

- very clear presentation with simple solutions
- point out that working at the virtual mapping layer of the storage system creates opportunities for more efficient implementation

## 3. Weakness (Limitations of the paper)

- none

## 4. Some Insights (Future work)

- background of disk encryption
  - data is encrypted before being written to disk
    - if the disk is stolen or illegally accessed, attackers would not be able to make sense of the data
  - encryption is done at a sector-by-sector granularity
    - 512 bytes or 4096 bytes
- disk encryption today
  - a unique data encryption key **per disk**
  - use sector number or LBA as the IV
    - only security concerns: overwrites to the **same address**

- virtual disk
  - contains a virtual-to-physical mapping layer: can piggyback on to augment the layout and incorporate additional per-sector information
- wide-block encryption
  - every bit of the plaintext of sector will influence the **entire** ciphertext of the sector
  - low performance and patenting considerations
- block storage systems would benefit by natively supporting per-sector metadata
  - for the long term
