---
typora-copy-images-to: paper_figure
---
# On Data Parallelism of Erasure Coding in Distributed Storage Systems
@ICDCS'17 @Data Parallelism
[TOC]

## Summary
***Motivation of this paper***: Data paralleism, which refers to the number of blocks that can be read by different processes simultaneously, is limited by existing **systematic** erasure code. This paper is designed to extend data parallelism from reading $k$ data blocks in parallel to reading all $n$ blocks. Therefore, it can have a higher overall throughput.
![1535080941866](paper_figure/1535080941866.png)

***Carousel Codes***: 
- Carousel codes achieve a flexible trade-off between **data parallelism** and **data availability**.
The process of the construction is shown below: 
![1535118132918](paper_figure/1535118132918.png)
- How to achieve flexible parallelism: This paper also allows users to flexibly specify the degree of data parallelism by controlling the number of blocks that contains the original data.


***Implementation and Evaluation***:
This paper implements the Carousel Code in C++. All operations, including encoding, decoding, and reconstruction are implemented by using ISA-L.
- The advantage of the **sparsity** of the generating matrix in Carousel codes
> Even though the size of the generating matrix is expaned the complexity of encoding and output bit does not change.

**Evaluation**
>1. The comparison of the encoding and decoding throughput for various values of $k$.
>2. Completion time of reconstruction operations for various values of $k$.
>3. Comparison of Hadoop jobs running on data encoded with systematic RS codes and Carousel codes. (terasort and wordcount in Hadoop)
>4. Comparsion of the time of retrieving a 3GB file from HDFS with systematic RS code and Carousel codes 


## Strength (Contributions of the paper)
1. This paper presents **Carousel Codes**, which has a higher data parallelism $\rightarrow$ a higher overall throughput.
2. It also shows the besides the MDS property and the configurable data parallelism, Carousel codes can achieve such optimal network transfer during reconstruction as well.
3. It also implemented Carousel codes in C++ and developed its prototype in Apache Hadoop.
## Weakness (Limitations of the paper)
1. I think a important issue of this code is the overhead of reconstruction, including the data re-distribution and re-encoding, which is much higher than the systematic RS code.
## Future Works
1. For the first weakness, I consider how to decrease this kind of overhead is a potential research direction.
