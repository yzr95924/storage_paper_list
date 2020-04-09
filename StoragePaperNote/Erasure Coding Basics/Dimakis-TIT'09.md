---
typora-copy-images-to: paper_figure
---

# Network Coding for Distributed Storage Systems

@Alexandors G. Dimakis @TIT'09 @Repair Bandwidth
[TOC]

## 1. Summary

This paper targets the question that

> How to generate encoded fragments in a distributed way while transferring as little data as possible across the network.
> Investigate the fundamental tradeoff between storage and repair network bandwidth.

It introduces the notion of **regenerating codes**, which allow a new node to communicate functions of the stored data from the surviving nodes. It calls codes that lie on the optimal tradeoff curve (storage and repair bandwidth) **regenerating codes**.

![1533832651101](paper_figure/1533832651101.png)

The **key point** of it is that:

> nodes do not send their information but generate smaller parity packets of their data, and forward them to the newcomer, who further mixes them to generate new packets.

The two extremal points on the tradeoff curve are **minimum-storage regenerating (MSR)** codes and **minimum-bandwidth regenerating (MBR)** codes 

### Information Flow Graph

This paper uses an **information flow graph** $G$ to describe how the information of the data object is communicated through the network, stored in nodes with limited memory, and reaches reconstruction points at the data collectors.

### Storage-Bandwidth Tradeoff
Its important observation is that:
> The minimum repair bandwidth is a **decreasing function** of the number $d$ of nodes that participate the repair. While the newcomer communicates with more nodes, the size of each communicated packet $\beta$ becomes smaller fast enough to make the product $d \beta$ decrease, as $d$ increases, and, therefore, minimal for $d=n-1$.

### Minimum-Storage Regenerating (MSR) Codes and Minimum-Bandwidth Regenerating (MBR) Codes

The two extremal points on the optimal tradeoff curve, which correspond to the best storage efficiency and the minimum repair bandwidth, respectively. 

### Evaluation
It compares regenerating codes with other redundancy management schemes in the context of distributed storage systems.
For **availability**:
A file is *available* when it can be reconstructed from the data stored on currently available nodes.
For **durability**:
A file's durability is maintained if it has not been lost due to permanent node failures
> it may be available at some point in the future.


## 2. Strength (Contributions of the paper)

1. It maps the code repair problem to a multicasting problem on the information flow graph.
2. It presents a general theoretic framework that can determine the information that must be communicated to repair failures in encoded systems
3. It identifies a tradeoff between storage and repair bandwidth.

## 3. Weakness (Limitations of the paper)

1. There are still many issues that remain to be addressed before these ideas can be implemented in practical systems.

## 4. Future Work

1. investigate deterministic designs of regenerating codes over small finite fields
2. how CPU processung and disk I/O will influence the system performance, as well as integrity and security for the linear combination packets.
