---
typora-copy-images-to: paper_figure

---

## Enabling Concurrent Failure Recovery for Regenerating-Coding-Based Storage System: From Theory to Practice
@MSST'13 @TC-2015 @Categroy
[TOC]
## 1. Summary
### Motivation of this paper:
This paper argues node failures are often **correlated and co-occurring** in practice. Thus, it is very necessary to minimizing the bandwidth for concurrent failure recovery, and it can also provide additional benefits. (e.g., delaying recovery).

This paper wants to investigate how to enable regenerating code to recover concurrent failures, <u>while retains existing optimal regenerating code constructions and the underlying regenerating coded data.</u>

### CORE
CORE is built on existing MSR code. It preserves the optimal storage efficiency of MSR. In its design, it does not fix the number $t$ of failure nodes.

#### Baseline Approach
In its baseline approach, the main idea is to divide packets into two categories: *real packets* and *virtual packets*
> 1. To recover each of the $t$ failed nodes, the relayer still operates as if it connects to $n-1$ nodes. It treats the packets downloaded from the failed nodes as virtual packets. 
> 2. It computes each virtual packet as a **function** of the downloaded real packets.
> 3. It can compose $t(t-1)$ equations based on the above idea, however, it needs to guarantee the **linearly independency** between each equations. And finally it can recover multiple loss packets by solving the equation system.
> ![1547437042911](paper_figure/1547437042911.png) 
> ![1547437239206](paper_figure/1547437239206.png)

#### For any Failure Pattern

In the baseline method, it needs the failure pattern to statisfy the constraints of linearly independency which cannot work in some failure patterns. So it also extend the baseline approach to deal with the case that cannot statsify linearly independency.
> Solution: include other surviving nodes to form a virtual failure pattern until it can statisfy the linearly independency (achieve the good failure pattern).
> ![1547449080194](paper_figure/1547449080194.png)

### Implementation and Evaluation
- It implements it with HDFS-RAID. For further speedup, it also applies a pipeline mode to leverages the multiple threads to parallel the encoding/decoding operations.
- Evaluation
> 20 Nodes, Compare with RS
> Decoding throughput, striping throughput, recovery throughput, degarded read throughput, Map-Reduce runtime with node failures.

## 2. Strength (Contributions of the paper)
- CORE just adds a new recovery scheme atop existing regenerating codes, it guarantees the generality.
- The implementation of CORE is not very complex, its relayer architecture can support regenerating code easily. It should be easy to follow. 
- This paper also integrates CORE in HDFS to test its practical performance.
- In this paper, it derives the experiment to investigate the bottleneck during the recovery operation. It observes zthe download step is the bottleneck which can support the need of minimizing recovery bandwidth.

## 3. Weakness (Limitations of the paper)
- It just considers the cases of Interference Alignment (IA) codes and Product Matrix (PM) codes. How about other kinds of regenerating codes? I think it should clarify this point.
- This paper does not provide a way to identify the bad failure pattern. 

## 4. Future Works
- Due to the fact that this paper does not provide the rationale behind bad failure pattern to cause the linearly dependency, I think one thing that can be done is to provide a genernal way to identify the bad failure pattern.
- In this paper, it fixes the quantitative relationship between parameter $(k,n)$ of regenerating codes, one thing can be done is try to investigate how the those parameters affect CORE. Can it reduce more redundancy? 