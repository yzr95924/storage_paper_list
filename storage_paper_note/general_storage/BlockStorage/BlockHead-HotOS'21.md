---
typora-copy-images-to: ../paper_figure
---
Don't Be a Blockhead: Zoned Namespaces Make Work on Conventional SSDs Obsolete
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotOS'21 | Zoned Storage |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - Industry has standardized and is adopting Zoned Namespaces (ZNS) SSDs, which offer a new storage interface that dominates conventional SSDs
    - argue for an immediate and complete shift in research to ZNS SSDs and discuss research directions

### ZNS SSD 

- ZNS devices dominate
  - Zoned namespace SSDs in six different states: empty, open, closed, full, ready-only, and offline
  - ZNS costs less per GiB
    - Less on-board DRAM: the FTL maintains address translations at the granularity of zones (e.g., 16 MiB)
    - Less overprovisioning: ZNS SSDs do not do GC, since the erasure blocks in a zone are always completely invalidated and erased when the zone is reset
      - system resources can be allocated according to an application's needs, **rather than being fixed for all applications**
  - ZNS is a more useful abstraction
    - writes may write to any zone, but each zone must be written sequentially. This thin interface gives applications more control over data placement on flash
      - more control over write amplification
    - since the host **has insight into application specifics**
      - can manage GC and other data management tasks **better than an on-board FTL**
  - ZNS may get better performance
    - the host is empowered to make **application-aware** data placement decisions. 
      - can control what data will be erased together by writing it to the same zone
  - ZNS SSD adoption
    - Btrfs and ext4 are close to running natively on ZNS SSDs
    - Linux provides the dm-zoned device mapper which emulates a standard block device on top of any ZNS SSD
    - The SPDK of developing kernel-bypass storage applications, supports ZNS as of version 20.10
- Research agenda
  - Improving performance
    - How can application-level information improve zone management?
      - software can often make educated guesses about data expiration 
        - pages in the same file are more likely to get deleted or overwritten together than pages in different files
    - How much can filesystem knowledge reduce write amplification?
    - How should applications interact with zones?
      - raw zoned storage access offers the most control over I/O and data placement
      - Interacting with zoned flash devices in a way that maximizes I/O performance, usability, and device endurance has not been fully explored
    - What is the best approach to I/O scheduling with host-driven device management?
      - with ZNS SSDs, the performance of individual zones is independent of one another, beyond sharing bandwidth.
    - How can we best exploit transparent data placement?
  - Managing limitations
    - How should hosts manage active zone limts?
      - current ZNS SSDs limit the number of zones that can be writable at the same time
      - it becomes a problem when a ZNS SSD is divided among applications that bypass the kernel
    - Are there workloads that perform worse on ZNS SSDs than on conventional SSDs?
      - zone's write pointer can suffer from lock contention. It is a problem for multi-writer workloads (writes are concentrated in a single zone)
    - How do ZNS SSDs fit with recent trends to offload I/O tasks from the host to dedicated hardware?



## 2. Strength (Contributions of the paper)

- provide some insights when shifting conventional SSDs to ZNS SSDs
  - includes some potential research topics

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- Conventional SSD background
  - flash cells in an erasure block can only be re-written after the block is fully erased
  - flash cells are hidden behind the traditional block interface 
    - FTL: exposes to the host **a flat address** space that can be randomly written at a page granularity (4 KiB), similar to HDDs
    - random writes force the FTL to implement garbage collection to reclaim space from old data that was overwritten in the logical address space
      - write amplification
  - applications have limited control over how the FTL arranges data on the device
- ZNS SSDs
  - a new SSD interface that dominates the conventional block interface in nearly every way
    - matching how data is written to physical flash (**abstracting the messy details of the flash hardware**)
  - A ZNS SSD is divided into logical regions called *zones*
    - writes can go to any zone but must be sequential within a zone
  - the FTL is thinner: coarser-grained address translation (at the erasure block), does not do garbage collection
    - performance variability and other impacts of GC are eliminated
  - Hosts control write amplification can make **application-aware data placement** and I/O scheduling decisions
