---
typora-root-url: paper_figure

---

# Clay Codes: Moulding MDS Codes to Yield an MSR Code
@FAST'18 @erasure code @Ceph @SMSR Codes
[TOC]

## Summary

***Motivation of this paper***: There is a strong need for erasure codes that can efficiently recovery from single-code. However, the conventional repair of an RS code is inefficient. This paper will deal with Minimum Storage Regeneration (MSR) codes, which has least possible repair bandwidth and three additional properties. It proposes the Coupled-layer (Clay) Code by extending the theoretical construction of Ye and Brag with practical considerations.

> 1. minimal disk read
> 2. minimize sub-packetization level $\alpha$
> 3. small field size, low-complexity implementation

***Clay Code Construction***: Here is the example with a (4, 2) scalar RS Code.
![1533545264630](/1533545264630.png)

![1533558772424](/1533558772424.png)

***Decoding***: For decoding, it introduces the notion of an Intersection Score (IS) to indicate the number of the vertices that correspond to erased bytes and which are at the same time colored red. Decoding is carried out sequentially, layer-by-layer, in order of increasing IS. 

For example: 
![1533559666806](/1533559666806.png)


***Implementation and Evaluation***: This work introduce the notion of **sub-chunking** to enable use of **vector erasure codes** with Ceph. For implementations of various MDS codes and Galois-field arithmetic, it uses **Jerasure** and **GF-Complete** libraries. 
In its experiments, it does evaluations in Amazon EC2. Those experiments are carried out on both **fixed** and **variable object-size** workloads. Measurements are made of:

> 1. repair network traffic
> 2. repair disk read 
> 3. repair time
> 4. encoding time
> 5. I/O performance for degraded, normal operations.

## Strength (Contributions of the paper)
1. introduce the construction of Clay codes 
2. do the modification of Ceph to support any vector code
3. the integration of Clay codes as a plugin to Ceph

## Weakness (Limitations of the paper)


## Future Work

