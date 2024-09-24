---
typora-copy-images-to: ../paper_figure
---
# Can ZNS SSDs be Better Storage Devices for Persistent Cache?

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'24 | ZNS SSDs, Cache |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - existing works mainly focus on cache data on block-based regular SSDs
    - widely used as storage backends for **persistent cache**
  - caching workload are **write- and update-intensive** with high capacity utilization
    - incurs a large amount of **device-level write amplification** (WA)
      - with many random and small writes to SSDs
    - internal garbage collection (GC)
      - SSD lifespan and performance issues
  - ZNS SSDs 
    - two advantages
      - need much lower internal over-provisioning --> larger capacity
        - a better overall cache hit ratio
      - new interfaces --> potential to reduce WA
- problem
  - <u>explore three possible schemes to adapt the existing persistent cache system on ZNS SSDs</u>
    - utilize **CacheLib** as a general cache framework

### ZNS SSDs in Persistent Cache

- three possible schemes
  - ![image-20240805014222417](./../paper_figure/image-20240805014222417.png)
  - **File-Cache**
    - run CacheLib on a ZNS-compatible file system (F2FS)
      - FS handle all low-level operations management
  - **Zone-Cache**
    - directly maps the cache on-disk management unit (i.e., region) to the fixed-size zone
      - achieve true zero WA and be GC-free
  - **Region-Cache**
    - a simple middle layer to translate the zone interface to the region interface
      - needs GC to clean the zones
- File-Cache
  - ZNS SSD can be formatted with a compatible file system
  - zone allocation, zone cleaning with GC, and indexing handled by FS
    - **fully transparent** to CacheLib
    - treat ZNS SSD like a regular device
  - bad
    - feasible and convenient, but will **bring explicitly high overhead**
- Zone-Cache
  - most of the persistent cache designs
    - group the newly inserted cache objects into a much larger management unit (fixed-size regions)
      - reduce WA and improve IO efficiency --> **allocating and evicting large IO units**
  - enlarge the region size to match the zone size
    - one region per zone
      - when a region is evicted, the zone can be directly reset without any data migration
        - real zero WA
        - GC-free
          - no OP is needed for GC
    - no extra indexing
      - adding one entry of zone number to the region metadata for IOs
  - bad
    - need to match the region to a large zone size (1077MiB in Western Digital ZNS SSD)
      - evicting a large region --> cause many valid or hot cache objects to be evicted
        - impact the hit ratio
    - need a larger region buffer in memory to cache the newly inserted objects
      - more DRAM space
    - long allocation time in eviction and a long filling time in insertion
      - reducing the parallelism effectiveness
- Region-Cache
  - add a simple middle layer to translate region to physical zone addresses
  - data management
    - region ID --> in-zone addresses
    - bitmap indicates whether the region is valid in zone
      - 1024 MiB Zone --> 16MiB region
  - GC
    - use a background thread to check the empty zone number and valid data size
      - GC threshold and the zone selection threshold are configurable
        - depends on the workloads
  - opens the design space to further optimize the throughput and WA
    - co-design between cache management and zone management

### Implementation and Evaluation

- evaluation
  - setting
    - flexibility, space efficiency, performance, and WA
      - compared with CacheLib on regular SSDs (**Block-Cache**)

    - ZNS SSDs
      - Western Digital Ultrastar DC ZN540 with 904 zones the zone size is 1077MiB

    - regular SSD
      - 1TiB SN540 SSD

  - overall comparison
    - ![image-20240807011652739](./../paper_figure/image-20240807011652739.png)
      - Zone-Cache has the largest cache size (no OP) --> highest cache hit ratio

  - different OP ratio
    - tradeoff between throughput and hit ratio
      - higher WA --> lower throughput

  - end-to-end evaluation with RocksDB
    - throughput: Region-Cache is highest, Zone-Cache is lowest
    - ZNS SSDs can give a larger cache size than regular SSDs


## 2. Strength (Contributions of the paper)

- ZNS SSDs persistent cache can reduce the tail latency and lower WA compared with regular SSDs
- ZNS SSDs can be better storage devices for persistent cache
- Zone-Cache can perform better in the **hit ratio**
- Region-Cache can perform better in **throughput**

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- open-channel SSDs
  - separate different data streams into different channels
    - relieving WA and GC penalties
- zone-based storage
  - sequential write and zone-based cleaning constraints
    - avoid internal GC
    - GC task can be managed by the applications
  - write pointer
    - shift to the start by ***zone reset***
    - jump to the end of the zone by ***zone finish***
- CacheLib
  - a pluggable caching engine developed by Meta
  - log-structured cache
    - flash space is partitioned into regions
    - each region is used to package cache objects with different sizes
  - **evict entire regions** rather than individual cache objects
    - region size is configurable, e.g., 16MiB
  - are designed to use either
    - a raw regular block device
    - one large file allocated in a file system (pre-allocated file)
