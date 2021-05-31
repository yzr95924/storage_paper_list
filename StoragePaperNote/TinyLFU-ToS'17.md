---
typora-copy-images-to: ../paper_figure
---
TinyLFU: A Highly Efficient Cache Admission Policy
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ToS'17 | Cache |
[TOC]

## 1. Summary
### Motivation of this paper
- Perfect LFU (PLFU): is an **optimal** policy when the access distribution is **static**
  - Limitations:
    - the cost of maintaining a complete frequency histogram for all data items ever accessed is prohibitively high.  
    - cannot adapt to dynamic changes in the distribution.	

### Method Name

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
- Locality
  - characterize the access frequency of all possible data items through a **probability distribution**
    - the probability distribution is highly skewed (a small number of objects are much more likely to be accessed than other objects).
- Least frequently used (LFU)
  - When the probability distribution of the data access pattern **constant** over time, it is easy to show that the LFU yields the highest cache hit ratio.
- LRU v.s. LFU
  - LRU can be implemented much more efficiently than LFU.
    - LRU can automatically adapt to temporal changes in the data access patterns and to bursts on the workloads.

