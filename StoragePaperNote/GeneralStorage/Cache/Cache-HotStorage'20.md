---
typora-copy-images-to: ../paper_figure
---
It's Time to Revisit LRU vs. FIFO
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'20 | Cache |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - Conventional opinion
    - while FIFO is much easier to implement, the improved hit ratio of LRU outweighs this.
      - several works went on to claim that from practical point of view, LRU is better than FIFO.
  - Changes
    - new caches such as front-ends to cloud storage have very **large scales** and this makes *managing cache metadata in RAM no longer feasible* 
      - main problem: large cache cannot hold their metadata in RAM
    - new workloads have emerged that possess different characteristics
  - Setting
    - An object storage service stores large, immutable objects in the cloud using a RESTful API.
      - typically suffers from **poor latency** and as such, holding a **front-end edge cache** can be very beneficial.

### LRU vs. FIFO

- FIFO
  - **drawback**: an item that was reused just recently might still be evicted from the cache if it was inserted a long time ago.
  - **management simplicity**: hold all the items in the cache in a queue and evict the next in line
    - with no need to update information about items already in the queue.
  - **key point**: LRU can no longer be assumed to be better than FIFO.
- Two main trend
  - A new scale to caches
    - the cache metadata associated with such a capacity can no longer fit in memory and need to spill over to persistent media
  - New workloads
    - a large public cloud object store (a new semantic behavior)
    - previous empirical caching studies may no longer hold for this new workload.
    - 158TB, IBM cloud object storage services (COS)
      - FIFO achieves hit rates that are very close to those of LRU, yet require a much smaller overhead for managing the cache.
- Large caches and cost model
  - cache metadata
    - the metadata refers to the information stored in order to find data in the cache and choose the right item to evict from the cache according to the cache policy.
    - LRU: **require updates of the metadata upon a cache hit**
      - update requires additional expensive random I/Os to the persistent storage
    - FIFO: Upon a cache miss, it evicts one or more objects from the head and inserts a object into the tail of the queue.
      - the list does not change upon a cache hit
  - cost model
    - a rough estimation of **overall latency** in an ideal setting where I/Os are performed sequentially
      - the latency of the cache and the remote backend are **fixed**, consider cache misses and cache hits

### Implementation and Evaluation

- Trace
  - MSR, SYSTOR, TPCC, IBM COS
  - handle variable sizes: break large objects into fixed size 4MB blocks and treat each one separately at the cache layer
- Evaluation
  - Hit rate comparison
    - LRU is often slightly better that FIFO in terms of pure hit rate
      - Yet many time, FIFO is similar or even better than LRU
  - Cost comparison
    - favors FIFO over LRU

## 2. Strength (Contributions of the paper)

- provide a new cache trace

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- on new traces, and under new media and cache settings, FIFO is actually a better choice than LRU.
- Smart paging
  - LRU metadata + RAM (locality)
- Frequency + recency + locality
