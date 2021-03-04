# Erasure Codes for Storage System: A Brief Primer

@James S. Plank @USENIX Login'13

[TOC]

## Summary

- This paper presents a primer on erasure coding as it applies to storage systems, and summaries some recent research on erasure coding.
The difference between **error correcting codes (ECC)** and **erasure code (EC)**.
> 1. In communications, errors arise when bits are corrupted silently in a message. This differs from an erasure, because the location of the corruption is **unknown**.
> 2. Erasures expose the location of the failure allows for erasure codes to be more powerful than error correcting codes (ECCs)

It first presents the **mechanics** of simple codes. The simplest erasure codes assume each disk holds one $w-bit$ word. When $w$ is larger, the arithmetic is called **Galois Field arithmetic**, denoted $GF(2^{w})$. For a disk, it holds more than a single $w-bit$ word. So it can be partitioned each disk into $w-bit$ words, and the $i-th$ words on each disk are encoded and decoded together, **independently of the other words**.

**Maximum distance separable (MDS)**: If a code successfully tolerates the failures of any $m$ of the $n$ disks, then the code is optimal with respect to fault tolerance for the amount of extra space dedicated to coding.
> MDS codes deliver optimal fault tolerance for the space dedicated to coding.

- RAID-4 and RAID-5
By using the **vector instructions**, a whole strip may be XOR'd together in units of 128 or 256 bits. (more efficient)
RAID-4: the identity of each disk is **fixed**.
RAID-5: the identities are **rotated** on a stripe-by-stripe basis. Therefore, the system is more **balanced**, with each disk equally holding data and coding.

- Linux RAID-6
RAID-6 systems add a second disk to a RAID-4/5 system and tolerate the failure of any two disks.

- Reed-Solomon Codes
Reed-Solomon Codes are **MDS** codes that exist whenever $n \leq 2^{w}$
> Reed-Solomon codes are important because of their generality: they exist and are easy to define for **any value of $k$ and $m$**.
> The CPU complexity of multiplication in a Galois Field is more expensive than XOR.

- Array Codes
Array codes were motivated by the desire to *avoid Galois Field arithmetic* and *implement codes solely with the XOR operation*. In an array code, each disk holds $r$ $w-bit$ words.
> the coding system may be viewed as an $r \times n$ array of words, where the columns od the array are words that are co-located on the same disk.

- Recent Work of EC
1. Reduced disk I/O for recovery
2. Regenerating Codes
> * Focus on reducing network I/O for recovery in distributed, erasure-coded storage systems. 
> * When one ore more storage nodes fail, the system replaces them, either with nodes that hold their previous contents, or with nodes that hold equivalent contents from an erasure-coding perspective. 
> > A properly defined regenerating codes has the surviving nodes read even more data from disk, but then they message it computationally so that they transmit even less data to perform regeneration.

3. Non-MDS Codes
The fault-tolerance of the non-MDS code is **not optimal** for the amount of extra storage committed to erasure coding. But the relaxation of the MDS property is typically accompanied by **performance improvements** that are impossible to achieve with MDS codes.
> The computational and I/O costs are smaller than an MDS system.

## Strength

1. This paper has presented how erasure codes are leveraged by storage systems to tolerate the failure of disks, which is very easy to understand.

## Weakness

none

## Future Work

none