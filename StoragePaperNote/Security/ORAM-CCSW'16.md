---
typora-copy-images-to: ../paper_figure
---
Oblivious RAM as a Substrate for Cloud Storage - The Leakage Challenge Ahead
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CCSW'16 | ORAM |
[TOC]

## 1. Summary
### Motivation of this paper
- ORAM Storage Interface 
the atomic unit of storage and access in ORAM algorithms has been the **block**. Allow a ORAM client to **read** and **write** to block addresses.
> the untrusted storage server cannot learn the plaintext of user content, the requested addresses, nor the relationships between requested addresses.

- This paper investigates the effects of the RAM interface mismatch between ORAM algorithms and cloud storage.
> the block-oriented interface of ORAM can be also problematic for cloud applications in terms of leakage.

### ORAM side-channel 
- Threat Model
the remote server is "honest but curious"
> correctly follows the protocol
> attempts to gain as much knowledge as possible by direct observation of the data access pattern.

- Metrics
1. Bandwidth Efficiency
$$
BE = \frac{\text{Total Data to access a set of file}}{\text{Total size of files}}
$$
![1570072565254](../paper_figure/1570072565254.png)

Given a set of $M$ files $F=\{f_1, f_2, ..., f_M\}$. It also considers the **access probability** of 
file $i$
$$
\sum_{i=1}^M P(f_i) =1
$$

2. Privacy Leakage
Current ORAM schemes operate at the block level. They may leak bits of information about the secret input when accesses occur at a **higher granularity (file)**.
> Every file access translates into a batch of random block access.

If the server sees a batch of size $b$ blocks, then it knows that this file belongs to class $F_b$, this can tell the server some information about the access pattern.
> measure the bit leakage by comparing the uncertainty of the server about $F$ before and after observing $B$
$$
\text{leakage} = \text{initial uncertainty} - \text{remaining uncertainty}
$$

It uses the posteriori probability to define this uncertainty.

The root cause of this leakage is the mapping between files and batches is **deterministic**.


- Tradeoff between information leakage and performance efficiency
Achieve different tradeoff points via varying the size of block.

- How to mitigate this information leakage
1. Maximizing block size (naive strategy)
restrict the size of all batches to 1 by choosing as the block size the size of the largest file.
> personal storage systems such Dropbox, exhibit high variability in file size. (fitted by heavy-tailed distributions)
> make it impractical to predict the size of the largest file in advance.


2. Periodic ORAM access
By accessing the ORAM at a periodic rate, it is possible to fully obfuscate the actual number of file blocks, because the server cannot tell **when** a request for a file starts and terminates.
> may harm the performance since high overhead
> how to set the periodic rate?

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)
1. this paper shows a formal definition of the information leakage in this problem.

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. This work also investigates how different block sizes affects the degree of information leakage in ORAM
> this can also extend to how different chunk sizes affects the degree of information leakage in deduplication system.
