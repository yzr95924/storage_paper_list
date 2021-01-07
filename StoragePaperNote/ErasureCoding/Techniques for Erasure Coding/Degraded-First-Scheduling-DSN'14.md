---
typora-copy-images-to: paper_figure
---
# Degraded-First Scheduling for MapReduce in Erasure-Coded Storage System
@DSN'14 @degraded read schedule
[TOC]

## Summary
***Motivation of this paper***: There remains an open issue of how to customize the data analytics paradigm (e.g. MapReduce) when they operate in failure mode and need to perform degraded read. 
> Based on the observation: while the local tasks are running, the MapReduce job does not fully utilize the available network resources.


***Degraded-first scheduling***
- **Main idea**: schedule some degraded tasks at the earlier stages of a MapReduce job and allow them to download data first using the unused network resources.
>1. to move the launch of some degraded tasks ahead to take advantage of the unused network resources, so as to relieve the competition for network resources among degraded tasks later.
>2. the default locality-first scheduling launches degraded tasks at the end, thereby making them **compete for network resources**. 

- This paper conducts simple mathematical analysis to compare the default locality-first scheduling and its basic degraded-first scheduling in terms of the runtime of a MapReduce job.
> provide preliminary insights into the potnetial improvement of degraded-first scheduling in failure mode.

***Implementation and Evaluation***:
**Implementation**:
This paper implements degraded-first scheduling by modifying the source code of Hadoop 0.22.0.
**Evaluation**: 
1. Comparison of MapReduce Runtime
> Single-job scenario vs Multi-job scenario

## Strength (Contributions of the paper)
1. This paper porposes the degraded-first scheduling, a new MapReduce scheduling scheme that improves MapReduce performance in erasure-coded clustered storage system operating in failure mode.
2. It also implements a discrete event simulator for MapReduce to explore the performance gain of degraded-first scheduling in a large-scale cluster.
3. This paper implements degraded-first scheduling on Hadoop and compare the performance of locality-first scheduling and degraded-first scheduling in a 13-node Hadoop cluster.

## Weakness (Limitations of the paper)
1. I think the idea of this paper is not very novel and intutive.
2. This experiment of this paper is not very enough.

