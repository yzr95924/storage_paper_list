---
typora-copy-images-to: paper_figure
---
# Cross-Rack-Aware Updates in Erasure-Coded Data Centers
@ICPP'18 @Cross-Rack-Aware @Parity Updates
[TOC]

## 1. Summary
### Motivation of this paper: 
- EC updates are very common in today's DC storage workloads (*update-intensive workloads*)
> Frequent small-size updates in turn lead to instensive parity updates in erasure-coded DCs
> **Hierachical topological nature** of DCs makes the cross-rack bandwidth often oversubscribed, and much more scarce thatn the inner-rack bandwidth.

- This paper tries to distribute a stripe ($RS(n, k)$) across $r<n$ racks, so that it can mitigate the overhead of cross-rack update traffic. 


### Cross-Rack-Aware Updates
- To exploit the opportunity of aggregating the updates of data or parity chunks:
> 1. it leverages the **append-commit procedure**, without immediately updating the associated parity chunk.
> 2. it appends the new data chunk to an **append-only** log, and executes the update in commit phase. (Note: there is no **redundancy** of this new update chunk $\rightarrow$ **interim replication**)
> 3. **Interim replication**: currently stores one replica for each newly updates data chunk in a different rack *temporarily*. (until it performs parity updates in the commit phase)
>
>    ![1546917876953](paper_figure/1546917876953.png)

- Change the delta-based update to **selective parity updates** 
> ![1546918149839](paper_figure/1546918149839.png)
>
> 1. The difference of those two method is where to compute the <u>change of a parity chunk</u> (the place where the parity chunk store, or the place where the data chunk store). (Note: it is not the theorically minimum cross-rack update traffic, because it ignores the case of some special erasure code)

- Data Grouping (based on the **spatial locality in updates**)
> 1. process each stripe independently (rather than multiple stripes), and only select two racks for data grouping.
> 2. **swap** (presever fault tolerence) updated data chunks and non-updated data chunks, reallocate the updated data chunks in the same rack, to mitigate the cross-rack update traffic. (Here is an algorithm to find the maximum gain of various swap schedules)
> ![1546932031713](paper_figure/1546932031713.png)

### Implementation and Evaluation
- Implementation
![1546937279046](paper_figure/1546937279046.png)

- Evaluation
> 1. Compare with baseline and PARIX. 
>> Recovery throughput, impact of non-buffered I/O, impact of gateway bandwidth, import of append phase time. 
> 2. To simulating a hierarchical DC, it uses a node to act as gateway. And use TC to limit the gateway bandwidth. so as to minic the over-subscription scenario.

## 2. Strength (Contributions of the paper)
- This paper presets CAU to mitigate the cross-rack update traffic through two key points
> 1. selective parity updates
> 2. data grouping


- Leverage the interim replication to maintain reliability, and do reliability analysis to prove it can work.

- Implement the prototype of CAU (**open-source**), and evaluates the CAU under real-world workloads. (including trace-driven analysis, local cluster experiments, Amazon EC2 experiments)
## 3. Weakness (Limitations of the paper)
- In its data grouping process, it swapes chunks of different racks, which also needs to updates the metadata of metadata server. 
- In part of reliability analysis, its model is very simple, and does not consider the cases of different, I feel it is not very convincible.

## 4. Future Works
- 1. it can further leverge some features of specifal erasure code to further improve its performance. For example, it may not require to update all parity blocks in some special erasure code.
- 2. In its data grouping, it just consider swap the block between two rack, if it extends the case to multiple racks, can it gain further improvement?
