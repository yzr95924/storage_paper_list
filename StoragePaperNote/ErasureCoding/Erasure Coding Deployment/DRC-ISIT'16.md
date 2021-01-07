---
typora-copy-images-to: paper_figure
---
# Double Regenerating Codes for Hierarchical Data Centers
@ISIT'16 @Regenerating Codes
[TOC]

## Summary
***Motivation of this paper***: Deploying erasure coding in data center remains challenging due to the hierarchical nature of data centers (multiple racks, each comprising multiple nodes for storage). And the cross-rack bandwidth is heavily oversubscribed. Thus, this paper's goal is to minimize the cross-rack repair bandwidth in hierarchical data center. 

***Double Regenerating Codes***
The core idea of DRC is to perform regeneration **twice**: first within a rack and the across multiple racks. So it calls this approach **double regeneration**.
> repair design targets the unbalanced nature of inner-rack and cross-rack capacities in hierarchical data centers.
![1537620570242](paper_figure/1537620570242.png)

- Double Regeneration makes two trade-offs:
>1. It can only tolerate a single-rack failure
>2. the sum of the inner-rack and cross-rack repair bandwidth is higher than that in MSR codes. (trade the inner-rack repair bandwidth for the cross-rack repair bandwidth)

- DRC exploits node cooperation within a rack to minimize the cross-rack repair bandwidth.

***Implementation and Evaluation***:
**Implementation**
None
**Evaluation**
Compared with RS, MSR, IEE

## Strength (Contributions of the paper)
1. prove that there exists a DRC construction that minimizes the cross-rack repair bandwidth for a single-node repair , while preserving the MDS property.
2. show via quantitative comparisons that DRC reduces the cross-rack repair bandwidth of state-of-the-art MSR codes by up to 45.5%.
## Weakness (Limitations of the paper)
1. DRC can only tolerate a single-rack failure (as opposed to the nomal code)
## Future Works
1. How about leveraging the special topological structure of rack-based data center to further reduce the repair overhead?