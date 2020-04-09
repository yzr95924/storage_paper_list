---
typora-copy-images-to: paper_figure
---
# NCCloud: A Network-Coding-Based Storage System in a Cloud-of Clouds
@FAST'12 @ Regenerating Code
[TOC]

## Summary
***Motivation of this paper***: It is important to do the repair in multiple cloud storage. This paper's objective is to minimize the cost of storage repair (due to the maintain of data over the clouds) for a permanent sing-cloud failure. 


***Functional Minimum-Storage Regenerating Code (FMSR)***:
This paper present a **proxy-based**, multiple-cloud storage system, called NCCloud, which can practically address the reliability of today's cloud backup storage. NCCloud implements FMSR, which eliminates the encoding requirement if storage nodes during repair, while ensuring the new set of stored chunks after each round of repair preserves the required fault tolerance.
![1534231209288](paper_figure/1534231209288.png)
Code chunk $P_i$ equals to linear combination of original data chunks. For the repair in FMSR, it downloads one code chunk from each surviving node and reconstructs new code chunks (via random linear combination) in new node.
However, FMSR codes is not systematic, FMSR codes are acceptable for **long-term** archival applications, where the read frequency is typically low.

**Iterative Repairs**:
FMSR codes regenerates different chunks in each repair, so it is necessary to ensure **MDS property** still holds even after iterative repairs. To achieve this, it also proposes a **two-phase** checking method.
>1. **MDS property check**: current repair maintains MDS property.
>2. **Repair MDS property check**: next repair for any possible failure maintains MDS property.


***Implementation and Evaluation***:
It implements NCCloud as a proxy that bridges user applications and multiple clouds including three layers:
>1. file system layer
>2. coding layer (both RAID-6 and FMSR)
>3. storage layer

**Evaluation**:
Response time: both in Local Cloud (OpenStack Swift) and Commercial Cloud (multiple containers in Azure) with different file sizes.

## Strength (Contributions of the paper)
1. design FMSR code that can be deployed in thin-cloud setting as they do require storage nodes to perform encoding during repair.
2. implement FMSR code with a two-phase checking scheme, which ensures that double-fault tolerance is maintained in the current and next round of repair.
3. conduct monetary cost analysis and extensive experiments on both local cloud and commercial cloud settings.

## Weakness (Limitations of the paper)
1. FMSR code is not a systematic code that it only stores encoded chunks formed by the linear combination of the original data chunks, and does not keep the original data. Thus, the overhead of frequent reading is high. 
2. In this paper, it just considers an FMSR code implementation with double-fault tolerance.  

## Future Work
1. consider how to reduce the encoding overhead of FMSR code
2. make the FMSR code more general, beyond double-fault tolerance


