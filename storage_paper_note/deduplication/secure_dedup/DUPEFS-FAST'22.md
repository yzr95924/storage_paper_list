---
typora-copy-images-to: ../paper_figure
---
DUPEFS: Leaking Data Over the Network With Filesystem Deduplication Side Channels
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'22 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - the implementation in today's advanced filesystems such as ZFS and Btrfs yields **timing side channels** that can reveal whether a chunk of data has been deduplicated
    - explore the security risks existing in filesystem deduplication
- main goal
  - use carefully-crafted read/write operations that show exploitation is not only feasible, but that the signal can be amplified to mount **byte-granular attacks over the network**
    - the main difference from previous secure deduplication work (memory deduplication):
      - filesystem operations tend to be **asynchronous** for efficiency
      - the granularity of filesystem deduplication (often as large as 128 KiB) is large

### DUPEFS

- threat model
  - an attacker who has direct or indirect (possible remote) access to the same filesystem as a victim, and the filesystem performs inline deduplication
    - local: using low-level system calls such as write(), read(), sync(), fsync()
    - remote: interacts with the filesystem through a program that is not under the attacker control
      - e.g., a server program
- challenges
  - **performance**: the I/O operations are mostly asynchronous to hide the latency 
    - filesystems cache data complicates the construction of a timing attack
  - **reliability**: even if data is deduplicated, the metadata still needs to be written to disk, which interferes with the timing channel
  - **capacity**: modern filesystems perform deduplication only across many blocks that are either temporally or spatially close to each other, clustered together in a deduplication record
    - increase the entropy of any target secret deduplication record
- data fingerprinting
  - relies on the general timed read/write primitive to **reveal the presence of existing known but inaccessible** data
- data exfiltration
  - allow two colluding parties with direct/indirect access to the same system to communicate over a stealthy covert channel
- data leak
  - alignment probing
    - stretch controlled data to fill the deduplication record minus one or more bytes of secret data
    - ![image-20220316134336877](../paper_figure/image-20220316134336877.png)
  - secret spraying
    - generate a stronger signal over LAN/WAN
    - spray candidate secret values over many deduplication records and issue many  writes for the corresponding guesses
- attack primitives
  - ![image-20220316134407739](../paper_figure/image-20220316134407739.png)

- mitigation
  - using pseudo-same-behavior
  - write path
    - even for duplicated data, it still overwrites existing on-disk data
    - slow down deduplicated write path
  - read path
    - introduce time jitter on the read path
    - enforce pseudo-same-behavior for disk access patterns

### Implementation and Evaluation

- evaluation
  - on FreeBSD for ZFS, and Linux for Btrfs
  - attack effectiveness
    - success rate
    - attack time
    - I/O
  - data fingerprinting, data exfiltration, data leak

## 2. Strength (Contributions of the paper)

- analyze filesystem deduplication side channels and differentiate it with previous work (asynchronous disk accesses and large deduplication granularities)
  - the attacker can mount byte-level data leak attacks across the network
- propose some light-weight mitigation for such attacks

## 3. Weakness (Limitations of the paper)

- the remote attack is based on the browser implementation and this is not very general 
- the mitigation approach is practical but cannot completely eradicate the signal

## 4. Some Insights (Future work)

- SHA-256 vs. faster hashing
  - it can also rely on faster hash functions that are not collision-resistant (such as **fletcher4**)
    - Since hashing may incur collisions, some implementations include an additional step to verify that the data inside the matching deduplication records is identical

- Deduplication granularity in filesystem deduplication
  - filesystems perform deduplication at a granularity that is **a multiple of the data block size**
    - a sufficient number of data blocks must be written to the filesystem to reach the deduplication record size
- the timed write primitive
  - the **timing difference** of handling unique data and duplicate data
    - process duplicate data is cheaper (only update the metadata)
  - allow attacker to leak whether certain data is present on the filesystem during a write operation
- the timed read primitive
  - duplicated data from different files end up in distinct physical memory pages 
    - as the page cache (in Linux) operates at the file level
  - if a block of a file becomes deduplicated, its physical location on the disk **differs from its surrounding blocks**