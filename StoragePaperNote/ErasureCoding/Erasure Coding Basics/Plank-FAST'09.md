#A Performance Evaluation and Examination of Open-Source Erasure Coding Libraries For Storage
@James S. Plank @FAST'09
[TOC]

## Summary
- This paper's goals are to 
> 1. compare codes and implementations to discern whether **theroy matches practice**
> 2. demonstrate how parameter selection can lead to good coding performance

It firstly demonstrates the **Nomenclature** of a Erasure Coded Storage System, then gives brief introductions of different kinds of Erasure Codes
> 1. *Reed-Solomon (RS) Codes*: strip unit is a $w-bit$ word, where $w$ must be large enough that $n \leq 2^w+1$ (Most implementations find $w=8$ performs the best). The multiplication in $GF(2^w)$ implemented with multiplication tables or discrete logarithm tables so that it is more **complex**. For this reason, Reed-Solomon codes are considered expensive.
> 2. *Cauchy Reed-Solomon (CRS) Codes*: this code extends RS codes in two points. First, the Generatir matrix uses Cauchy matrices instead of Vandermonde matrices. Second,
it eliminates the expensive multiplications of RS codes by converting them to extra XOR operations.

Next, this paper tests five open source erasure coding libraries (i.e., **Luby**, **Zfec**, **Jerasure**, **Cleversafe**, **EBENODD/RDP**) and do the **Encoding Experiment** and **Decoding Experiment** to measure their performances. When it does the experiment, it uses speeds of **memcpy()** and **XOR** as the baseline.
> 1. Encoding Time VS. Data Buffer Size: using **gettimeofday()** to measure the times of all coding activities.
> **Conclusion**: the size of the data buffer can impact performance, with the I/O removed, the effects of modifying the buffer size are negligible.
> 2. Encoding Speed VS. Packet Size: **lower** packet sizes have less tight XOR loops, but better cache behavior. **higher** packet sizes perform XORs over larger regions, but cause more cache misses.
> **Conclusion**: higher packet sizes perform better than lower ones. It also performs **a search algorithm** to find optimal packet size.

In addition, it also mentions that **the unit of XOR** used by the encoding/decoding software should match the <u>the largest possible XOR unit of the machine</u>.

In Conclusion, it summaries:
> 1. how to choose $w$ in RAID-6 code, CSR, RS.
> 2. the packets sizes of the codes should be chosen to yield **good cache behavior**.
> 3. the implementation must pay attention to **memory and cache**. It will be paramount for high performance.

## Strength
1. This paper presents a full evaluation and examination of the current Open-Source Erasure Coding Libraries in Storage System, which can help the readers get a great perspective of erasure codes.
## Weakness
Because this paper just evaluuates the Open-Source Erasure Coding Libraries, so it cannot say that there exists obvious weakness in this paper.
## Future Work  
1. This paper mentions that the modern architectures shift more universally toward **multicore**. So it can further investigate how open source libraries to exploit the opportunities of multiple processors on a board. (this is an additional challenge) 
2. More research needs to be performed on **special-purpose codes** beyond RAID-6, and implementations need to take advantage of the special-purpose codes that already exist.