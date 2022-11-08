---
typora-copy-images-to: paper_figure
---
# On the Speedup of Single-Disk Failure Recovery in XOR-Coded Storage Systems: Theory and Practice
@MSST'12 @speed up the recovery 
[TOC]

## Summary
***Motivation of this paper***: While enumeration recovery can find the optimal recovery solutio n for a single-disk failure in any XOR-based erasure codes, <u>it has a very high computational overhead.</u> Thus,*enumeration recovery* is **infeasible** to deploy (Limited hardware resources, Remote recovery scenario, Online recovery scenario). This paper seeks to minimize the number of symbols being read (or I/Os) from the surviving disks for the single-disk failure recovery and its objective is **the single-disk failure recovery**.  

***Replace Recovery Algorithm***:
The bottleneck of enumeration recovery is the huge search space of recovery equations. Thus, its goal is to achieve following objectives 
>1. Search efficiency
>2. Effective recovery performance 
>3. Adaptable to heterogenous disk capabilities

In its simple recovery model, there exists a recovery solution that contains exactly $w$ parity symbols for regenerating $w$ lost data symbols for each stripe in a single-disk failure. In summary, its replace recovery algorithm is:
>replace "some" parity symbols in a collection of $w$ parity symbols with the **same number** of parity symbols in the set of parity symbols in the $i-th$ parity disk (r epeat by resetting it with other parity symbols)

It also needs **a primitive function** that determines if the collection set is **valid** to resolve the $w$ data symbols after being replaced with other parity symbols in the collection of $w$ parity that are considered be included in the collection. And the complexity of total search is $O(mw^3)$, which is **polynomial-time**.

Besides this basic approach, this paper also mentions how to **enhance the likelihood** for its replace search to achieve the optimal point.

***Evaluation and Implementation***: It implements a **parallel recovery architecture** that parallelizes the recovery operation via multi-threaded and multi-server designs.
![1533909218190](paper_figure/1533909218190.png)
Evaluation:
>1. Impact of chunk size
>2. Recovery time performance 
>3. Parallel recovery

## Strength (Contributions of the paper)
1. It proposes a **replace recovery algorithm**, which uses a hill-climbing (greedy) approach to optimize the recovery solution.
2. From a theoretical perspective, this paper proposes a replace recovery algorithm that provides near-optimal recovery performance for STAR and CRS codes, while the algorithm has a polynomial computational complexity
3. From a practical perspective, it designs and implements its replace recovery algorithm on top of a parallel recovery architecture for scalable recovery performance.


## Weakness (Limitations of the paper)
1. This paper just consider the case that minimizing the number of symbols read. How about other optimization objectives. 
2. This paper just consider the case of single disk failure, how about an arbitrary number of disk failures

## Future Work
1. This paper can evaluate the performance of its replace recovery approach for other optimization objectives, and explore its applicability for general XOR-based erasure codes.
2. How to extend this method to the case of an arbitrary number of disk failures is also a direction


