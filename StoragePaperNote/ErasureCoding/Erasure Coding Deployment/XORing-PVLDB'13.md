---
typora-copy-images-to: paper_figure
---
# XORing Elephants: Novel Erasure Codes for Big Data
@PVLDB'13 @New Erasure Code @Repair problem
[TOC]

## Summary
***Motivation of this paper***: This paper wants to overcome the high repair cost of RS code. It presents a novel family of erasure codes that are efficiently repairable and offer higher reliability compared to Reed-Solomon codes. 

***Locally Repairable Codes (LRC)***: 
- The main idea of this code is to sacrifice storage efficiency to gain well performance. And it is inspired by "MDS code with parameters $(k, n-k)$ cannot have locality smaller than $k$". Because it is exactly the cost of **optimal fault tolerance**. Thus, it tries to construct a **near-MDS** code with non-trivial locality.
- LRCs are constructed on the top of MDS codes: MDS encoded blocks are grouped in logarithmic sized sets and then are combined together to obtain parity blocks or **logarithmic** degree. For the distance:
$$
\lim\limits_{k \rightarrow \infty} \frac{d_{LRC}}{d_{MDS}}=1
$$
It makes repair efficient by adding additional **local parities**.
![1534514760021](paper_figure/1534514760021.png)

***Implementation and Evaluation***:
Its system is a modification of **HDFS-RAID** that incorporates LRC. The **RaidNode** and **BlockFixer** classes were also subject to modifications. 
**Evaluation**: both in Amazon's Elastic Compute Cloud (EC2) and a test cluster in Facebook
>1. HDFS Bytes Read: total amount of data read 
>2. Network Traffic: total amount of data communicated from nodes (Amazon's AWS Cloudwatch monitoring tools)
>3. Repair Duration: the time interval between the starting time of the first repair job and the ending time of the last repair job.

## Strength (Contributions of the paper)
1. introduce a now family of erasure code called **Locally Repairable Codes** (LRCs), which are efficiently repairable both in terms of network bandwidth and disk I/O.
2. present both randomized and explicit LRC constructions starting from generalized Reed-Solomon parities.
3. design and implement a module that replaces Reed-Solomon codes with LRCs in HDFS-RAID.
4. The part of **impartance of repair** is well-written
## Weakness (Limitations of the paper)
1. The disadvantage of adding these local parities is the extra storage requirement
## Future Work
1. For the part of Reliability Analysis, this paper cannot solve the issue fo measuring the availability tradeoffs of coded storage systems. Thus, this can be a future research direction.

