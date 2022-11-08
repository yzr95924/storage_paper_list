---
typora-copy-images-to: paper_figure
---

# EC-Store: Bridging the Gap Between Storage and Latency in Distributed Erasure Coded Systems

@ICDCS'18 @Reduce data retrieval times @straggler problem

[TOC]

## 1. Motivation

- The performance difference between replication and erasure coding is primarily due to the time it takes to **retrieve data**, which dominates overall response times.
> The reason for retrieval costs are higher for erasure-coded storage systems because the effects of stragglers: The time taken to retrieve the slowest, or straggling, data chunk dominates retrieval time.
> Straggler: it must wait for multiple requests to complete

- It motivates a fault tolerant storage system that can achieve the best of both worlds: 
> 1. low storage overhead 
> 2. low latency data retrieval

## 2. Overview of this paper

### **Main Idea** of this paper
1. To solve the straggler problem: intelligently select chunks to retrieve so as to avoid stragglers
> This dynamic data access strategy uses chunk location information to generate a cost-effective strategy on-the-fly for data retrieval.
> By this way, it can reduce data access latencies and satisfy its best of both worlds goal.

2. It takes advantages of workload that **contain multi-item retrievals**. EC-Store leverages there correlated access patterns to dynamically co-locate data items that are accessed together, thereby reduce the chunk retrieval times.
> The items in multi-item requests are often correlated by application semantics.
> EC-Store considers the location of the chunks and their correlations to generate effective <u>data placement</u> and <u>flexible data access plans</u> that enable efficient data retrieval.

### **Contributions** of this paper
1. denmonstrate how the performance of erasure-coded storage systems cna be improved significantly through **dynamic data placement and data access** to mitigate retrieval costs.
> formulate the data access and movement strategies as **cost functions** with the aim of minimizing expected cost.

2. present a efficient design and implementation of EC-Store

3. detailed evaluation of EC-Store using 
> the Yahoo! Cloud Serving Benchmark
> a real workload trace of Wikipedia image accesses 

### The Method

- Goal: Minimize the expected retrieval time
> Reduce the number of distributed requests by accessing sites that have co-located data.

1. Estimating Data Access Cost
The data access and movement strategies rely on descriptions of **system state**:
- consider the state of the system to be a $numBlocks \times numSites$ **matrix** $C$ with entries $C_{i,j}$ representing the chunk placements.

Modelling Access Costs $cost(Q)$, $Q={B_i}$: two key factors
> 1. the overhead of accessing a remote site.
> 2. the retrieval cost of each request chunk at that site.

Satisfying Access Constraints: 
A client should minimize the cost of access while satisfying the constraints imposed by **system state**, **the request**, and **the erasure coding parameters**.
To minimize $cost(Q)$ over all vaild combinations of the **binary decision variables** (**Integer Linear Programming (ILP) 

> can use existing tools to compute an optimal solution
> $cost(C, Q)$ depends not only the query $Q$ but also on the state of the system $C$: 
> $$
> cost(C, Q)=\mathop{min}\limits_{C} cost(Q)
> $$
>

2. Estimating Chunk Movement Gain
As data movement affects data access, it uses the cost model to estimate access costs after movement by computing **the expected performance gains** from moving the chunk.
> By quantifying the effects of movement, it is able to compare multiple data effects of movement strategies and select the best strategy for execution.

- Change in Data Access Costs
It considers two important factors when measuring how moving a chunk affects the system.
>1. How movement affects **data access costs**
>2. How movement affects the distribution of load to sites that store data.
Merging these factors into a <u>single cost function</u>.


- Quantifying System Load
Consider the effect of moving a chunk on the load of a system
To avoid the effects of skewed load, it strives to distributed load evenly across sites. It promotes data movement from heavily loaded sites to lightly ones.
> model site load before and after data movement to quantify load balance improvements
> 1. CPU utilization 
> 2. I/O load

- Normalizing System Load
To present the degree to which a site has diverged from the average load, it defines a site **load balance factor**. 

- Estimating Change in System Load
Consider the effect of load created by data movement on both <u>the source and destination site</u> by using the load balance factor of the most imbalanced site.

- Estimating Benefit from Chunk Movement
Combine the two factors with **weight parameters**:
>1. effect of data access
>2. effect on load

3. Selecting Chunks for Movement
Employ a heuristic strategy for generating movement plans. Its heuristic is guided by two principles:
>1. Recently accessed blocks are likely to be accessed again.
>2. Sites which are under heavy load contribute to the straggling  chunk problem

### Implementation

- C++
- remote procedure calls: Apache Thrift library
- encoding and decoding blocks: Jerasure 2.0 library

### Experiment
- Objective:
>1. how data placement and access strategies improve performance
>2. verify its system operates with low overhead

- Benchmark:
> YCSB-E workload
> Wikipedia image accesses

- Experimental Results
> Response time: 
> Effect on Load: Read I/O; I/O load (the amount of data read per site)
> Block Size: expriment with varying block sizes, both smaller (10 KB) and larger (1 MB) 
> Resource Consumption: Memory, CPU, Network

## 3. Strength and Weakness
The strengths:
1. It proposes a dynamic data placement and data access method to reduce the response time by using a model to describe the system state and load. And this model is reasonable.
The Weaknesses:
1. This model is based on just considering the effect of *moving a single chunk at a time*.
> this model is not very complex.
2. The overhead of recording the access pattern and tracking access history are very high which effects its scalability

## 4. How to extend this paper
For those two weaknesses:
1. we can consider how to extend its model to the cases of moving multiple chunks at a time
2. How to record and learn the access pattern and access history with a low overhead