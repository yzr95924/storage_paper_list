---
typora-copy-images-to: paper_figure
---

# Lazy Means Smart: Reducing Repair Bandwidth Costs in Erasure-coded Distributed Storage
@SYSTOR'14 @Reconstruction Problem
[TOC]

## 1. Motivation

- **Excessive network demands of recovering data after node failures** hinders the broader adoption of erasure codes in cold-storage DSS. (grows with system scale)
> e.g. Recovery of a single data block in an RS(n, k) erasure coded storage entails transferring $k$ blocks from $k$ surviving nodes over the network. (**k-fold increase**)

- A common practice to cope with the growing network traffic is by **throttling the network bandwidth** available for recovery tasks.
> **Drawback**: This increase the number of degraded stripes in an uncontrolled manner, which in true dramatically affects read performance and data durability.

- This paper wants to <u>reduce the volume of network recovery traffic in an erasure-coded DSS to the level of 3-way replication but without loosing the durability advantages of the former.</u>

## 2. Overview of this paper

### **Main Idea** of this paper
The recovery of degraded stripes can be delayed as long as the risk of data loss remains tolerable. Thus, it can leverage **a non-linear tradeoff** between:
(1) recovery bandwidth
(2) the probability of data loss (durability and availability)
This paper goal is to <u>*make the steady-state bandwidth requirements of erasure coded storage commensurate with that of replicated storage, while preserving the failure resilience advantages of erasure coding*.</u>

### **Challenges** of this paper

How to design a practical mechanism to put the system into desired operating point on the tradeoff curve? (using new lazy recovery scheme)

For evaluation, it is not practical to run a prototype and measure those metrics.

### **Contributions** of this paper

1. A new mechanism for enabling significant repair bandwidth reduction in erasure coded storage.
2. A detailed DSS simulator ($ds-sim$) for evaluating long-term durability, availability and recovery bandwidth requirements of various storage schemes.
3. Evaluation of the bandwidth requirements of different storage schemes.

### Lazy Recovery
The intuition behind this approach is based on the figure below (use **Markov Chain Model** to generate):
![1526027787768](paper_figure\1526027787768.png)

#### 1. Lazy Recovery for all

*Main Idea*: Postpone the reconstruction of failed block until the number of available blocks in a stripe  reaches a given recovery threshold $r$

*Advantages*:
1. recovery two blocks for almost the same network cost as recovering one
> Same read times, different write times.

2. If a block is unavailable due to a transient event, delaying its recovery allows it more time to come back on its own.
> Avoid a redundant repair

*Drawback*:
1. It is not efficient enough for a DSS
> decreasing the recovery threshold by one may dramatically increase the number of degraded stripes.

#### 2. Lazy recovery only for transient failures

It refines the Scheme 1 to distinguish between **permanent disk failures** and **transient machine failures**.
> a. permanent failures: repair immediately
> b. transient failures: handle lazily	

*Advantage*:
1. perform better efficacy than Scheme 1
    *Drawback*:

2. need extra information to distinguish permanent events

3. cannot provide fine-grained control over the choice of the operating point of the tradeoff function.

#### 3. Dynamic recovery threshold
  The idea is to dynamically adjusting the recovery policy depending on the state of a whole system.
> It introduces a system-wide limit on the number of degraded stripes with permanently lost blocks and dynamically change its value.

*Drawback*:
1. This scheme needs the state of a whole system. Thus, it can work in a **centrally-managed well-maintained** DSS but might be <u>unrealistic</u> in a much less controlled peer-to-peer storage environment.

### Experiment
Using **Markov Model** for durability estimation and do two experiment:
> Experiment 1: compare the storage requirements and repair bandwidth of lazy recovery versus non-lazy schemes
> Experiment 2: compare the probability of data loss, unavailable data and the portion of degraded stripes of lazy recovery versus non-lazy schemes.

### Related Work
1. Erasure Coding
> Reed-Solomon codes

2. Bandwodth-efficient Erasure Coding
> Regenerating codes
> Local Reconstruction Codes (LRC)
> Piggiback Codes

3. Lazy recovery in storage system
> TotalRecall

## 3 Strength and Weakness
The strengths:
1. the idea of this paper is not very hard to understand. That is, do the tradeoff between repair bandwidth cost and the possibility of data loss
2. It dynamically adjust the recovery threshold for permanent failures depending on the whole system state, while performing lazy recovery of transient ones.
3. It provides a good way based on Markov Model to simulate its method.

The Weaknesses:
1. I suppose its idea is not very novel
2. It needs to collect extra information of the whole system state to adjust recovery threshold, that brings extra overhead and just can be achieved in centrally-managed well-maintained DSS. How about others?
3. It does not demonstrate a quantitative method of adjusting threshold, how to set threshold when factors are given?   
4. This approach may not be applicable because it puts the reliability of the data at risk.

## 4. How to extend this paper
1. how about combining the method with some new coding schemes?
2. how to made this method more realistic? Such as minimizing the overhead of collecting extra information
3. how to quantitatively control the threshold based on given factors?