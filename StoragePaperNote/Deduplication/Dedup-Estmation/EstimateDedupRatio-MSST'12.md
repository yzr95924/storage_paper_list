---
typora-copy-images-to: ../paper_figure
---
Estimation of Deduplication Ratios in Large Data Sets
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| MSST'12 | Deduplication Estimation |
[TOC]

## 1. Summary
### Motivation of this paper
This paper studies the problem of accurately estimating the data reduction ratio achieved by deduplication and compression on a *specific* data set.
> this paper focuses on what can be done when scanning the entire data set.
> using RAM as little as possible
> without reading most of the actual data.

The method of this work can be used to:
>1. estimating the number of disks to buy
>2. choosing a deduplication technique
>3. deciding whether to dedupe or not dedupe

### Method Name
- Key assumption
This paper studies what can be done under **essentially the whole data set is scanned**.
> how to estimate the deduplication and compression in an efficient manner.

1. The main bulk
reading data from disk, computing the hash, running a compression algorithm and updating the index table.
> heaviest: access to the disk, and compression.

- A general framework 
1. Sample phase
the sample is taken at random where each element appers independently.
> Hash values and compression rates are computed for each element in the sample.

From the entire dataset, it would choose $m$ elements (a configurable parameter in advance), and compute the corresponding hash vale and add it to a set as the base sample.
> record the count

**Rationale**: in this paper's case, an element that two replicas in the dat set has double the probability of being included in the base sample.

**Sample Method**:
> 1. choose $m$ random numbers in $\{1, ...,n\}$
> 2. generate a random number according to binomial distribution.

2. Scan phase (used to derive the data-reduction ratio estimate)
store statistics only about elements in the base sample.
> The hash is computed for each element but it is only recorded if it matches a hash of an element in the base sample.

The entire dataset is scanned, for each element, its hash signature is computed.
> If this signature is matched in the base sample, then the corresponding count is incremented by 1.

It finally estimates the 
$$
Est = \frac{1}{m} \sum_{i \in Sample} \frac{BaseCount_{i}}{Total_{i}}
$$

- Full file deduplication 
deduplication is only done between identical files
> achieves less than optimal deduplication ratios, yet it is easy to implement and can perform sufficiently well in some workloads.

1. Sampling files
> In this paper, the actual data needs to be read only for a small fraction of the file, which is related to the base sample.
> In handling of files, each such byte has independent probability $\frac{m}{N}$, where $N$ is the total number of byte in the dataset.

2. Scan optimization
hash on the first block of the file
> in many file system, the first block resides in the i-node of the file.
> thus can be read quickly during a metadata scan without the addition of extra disk seeks.

3. Using the following information
> 1. the length of the file
> 2. A hash signature of the first block of the file.

If those two messages of a file can match, then compute the full hash of this file, and count the frequency.

### Implementation and Evaluation
- Implemention
the scan phase can be run in parallel on a distributed system. The parallel execution would each node will do the scan locally and accumulate the count for the data adjacent to this node.


- Evaluation
1. Dataset (4 datasets)
personal workstations, enterprise file system repository, and backup date of two types.
2. Empirical accuracy
Sample size vs. relative error 


## 2. Strength (Contributions of the paper)
1. This paper sets the grounds on the limitations and inherent difficulties of sampling techniques.
> both analytical and in practice
> does not suffice to distinguish if the replication is a local phenomena or a global one.


## 3. Weakness (Limitations of the paper)
1. The method of this paper still needs to pass the whole dataset twice, which may incur the overhead of disk I/O.


2. the higher the data-reducation ratio is, the harder it becomes to give an accurate estimation.
> In order to determine the sample size, this method needs to give a bound of deduplication ratio 

## 4. Future Works
1. This paper mentions that it is impossible to predict the deduplication ratio accurately by looking only at a random subset of the data.
> can give arbitrarily skewed results.
> whether randomly or according to various sampling methodologies.

2. This paper also mentions it can incorporate major knowledge about the structure of the data to smartly sample it can then extrapolate what the overall ratio yet perform this efficiently with limited resources.
> "educated-sampling"

3. This problem can be classified into the question of estimating the number of distinct elements in a large collection of elements.

4. This paper argues that the knowledge of the total number of chunks in the data set is **essential** in the sampling process.
> the overall size of the data set can be computed by a standard traversal of the file system
> e.g., the unix $du​$ command

5. This paper mentions how to sample the file with variable-size chunking:
> the sampling should choose exact offsets in the file, and then choose the chunk which contains this offset.
> this can relieve the need to read entire file