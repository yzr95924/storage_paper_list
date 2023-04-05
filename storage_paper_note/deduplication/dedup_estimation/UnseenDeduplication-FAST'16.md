---
typora-copy-images-to: paper_figure
---
Estimating Unseen Deduplication - from Theory to Practice	
------------------------------------------
|  Venue  |        Category        |
| :-----: | :--------------------: |
| FAST'16 | Sample + Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
This paper intends to solve the problem of deduplication estimation, that is given a large dataset and try to understand the potential benfit from deduplication.
> Potential customers need this information in order to make informed decisions on whether high-end storage with deduplication is worthwhile for them.

The difficulty stems of deduplication estimation: deduplication is a **global** property
> naive way: need to search across large amounts of data.
> read a large fraction of the data from disk.

State-of-art countermeasures:
1. vague estimation based on prior knowledge on workload
> is highly inaccurate in reality (varying from a long range)

2. Scan the entire dataset
> requires a large amount of memory and disk operations 

The main challenges are to make deduplication algorithms actually applicable and worthwhile in a real world scenario.

### Estimating Unseen Deduplication
In this work, they study the ability to estimate deduplication while not reading the entire dataset.

- The key questions in this method:
> 1. understanding the estimation accuracy: what sample size is enough to achieve a good accuracy?
> 2. how to run the estimation algorithm with low memory consummption
> 3. how to combine deduplication and compression

- In this work, it terms a key concept called **Duplication Frequency Histogram**
Based on DFH, it designs the unseen algorithm to estimate the DFH of a dataset 
> from the DFH, it devises an estimation of the deduplication.
> Can take a DHF of a full set or a sample of the set
![1555770525391](paper_figure/1555770525391.png)


- The Unseen Algorithm 
1. Input: the observed DFH $y$ of the observed sample $S_p$ ($S_p$ determines the expected matrix transformation)
2. Process: find an estimation $\hat{x}$ of the DFH of the entire dataset $S$, which hash **minimal distance** from y
3. Output: dedupe ratio according to $\hat{x}$
> Rationale: ![1555765487674](paper_figure/1555765487674.png) 

4. distance: use a normalized $L1$ Norm

- Gauging the Accuracy (Range Unseen algorithm)
1. 5% to 15% is very sufficient for accurate estimation. (for a range)
2. To solve the question that how to interpret the estimation result and when sample is enough 
> It proposes a new approach that can return **a range of plausible deduplication ratio** rather than a single estimation number
> returns upper and lower bounds (the actual ratio lies inside the range)
>
> ![1555769164180](paper_figure/1555769164180.png)

3. Add two linear programs to find $x_{min}$ with the minimal dedupe ratio, and $x_{max}$ with the maximal dedupe ratio.
4. Need set a slackness parameter $\alpha$ (small $\rightarrow$ tighter estimation range)

- Sample Approach
Issue: when the sample rate is high, it needs to keep the duplication frequencies of all distinct elements in the sample $\rightarrow​$ the memory overhead would be high (GB)

To slove this, it proposes two approaches with around 10MBs of RAM:
1. Base sample approach: add another process of estimation (increase slackness parameter)
> Shortcoming: the dataset to be studied needs to be set in advance. (not dynamic) 

2. A streaming approach: using stream algorithm towards distinct elements evaluation with low memory. 
> Only the C chunks that have the highest hash values

- Estimating Combined Compression and Deduplication
Add the weight in DFH: compression weight.

- How to sample in the read work?
1. Sample requirement
> 1. sample uniformly at random over the entire dataset 
> 2. without repetitions 
> 3. using low memory 
> 4. gradual sample
> > sample a small precent, evaulate, then add more samples if needed 
>
> 5. faster than running a full scan (scan time dominates the running time)

2. Sample Size vs. Sampling Time
mitigate the drop off in short random reads in HDDs:
> 1. sample chunks: using larger chunk sizes ($4KB \rightarrow 1MB$ )

3. Sample Strategy
Simple way: use a fast hash function to hash the chunk ID, and ensure the output range is [0, 1), use this output to ensure whether to add this chunk to the sample.
> Not the cryptographic hash like SHA-1
> To ensure the overhead of sample is negligible.



### Implementation and Evaluation
- Implementation
The core techniques in Matlab, evaluation is based on various real life datasets


## 2. Strength (Contributions of the paper)
1. The intuition of this method is very simple, that instead of computing the convergent result, it just concerns the range of the deduplication ratio under a given sample rate. This is sufficient for practical propose, and can save the time of repetitions .
2. It also considers the issue of memory overhead of sampling when the sample rate is relativly high (15%)
## 3. Weakness (Limitations of the paper)
1. For the range unseen algorithm, I think it is not very reasonable to replace the original unseen algorithm.

2. In its evaluation part, it does not provide the part of deduplication performance. Just present the estimation range with different configuration.

## 4. Future Works
This work gives me the insight that can use a sample of the workload to estimate the whole workload property. This can be used in many scenarios