---
typora-copy-images-to: ../paper_figure
---
A Study of Practical Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'11 | Deduplication workload analysis |
[TOC]

## 1. Summary
### Motivation of this paper
Deduplication can work at either the sub-file or whole-file level.
> More fine-grained deduplication creates more opportunities for space savings.
> Drawback: reduces the sequential layout of some files, impacts the performance when disks are used for storage.

This paper consists of 857 file systems spanning 162 terabytes of disk over 4 weeks.
> from a broad cross-section of employees

This paper also conducts a study of metadata adn data layout. 
> 1. storage being consumed by files of increasing size continues unabated.
> 2. file-level fragmentation is not widespread.

### Method Name

- Use salted MD5 as its hash algorithm
Truncate teh result to 48 bits 
> in order to reduce the size of the data set.
> had the largest number of unique hashes, somewhat more than 768M, expect that about two thousand of those (0.0003%) are false matches due the truncated hash.

- Post processing
As an optimization, it observes the actual value of any unique hash was not useful to its analysis. (i.e., hashes  of content that was not duplicated)
> Novel 2-pass algorithm：
> First pass: if it tried to insert a value that was already in the Bloom filter, then it inserts it into a second bloom filter of equal size.
> Second pass: comparing the each hash to the second bloom filter only, if it was not found in the second filter, it was certain that the hash had been seen exactly once and could be omitted from the database. (very simple)

- Consider different deduplication domain
1. Deduplication in primary storage


2. Deduplication in backup storage
Performance in secondary storage is less critical than in that of primary, so the reduced sequentiality of a block-level deduplicated store is of lesser concern.


- Metadata analysis
This paper analyzes the file systems in terms of 
> age, capacity, fullness
> the number of files and directories


- File times
This paper shows that most files are modified between one month and a year ago, but about 20% are modified within the last month.

- On-disk layout
The behavior and characteristics of magnetic disks continue to be a dominant concern in storage system.
> It argues that it is not true that file system performance changes over time, largely due to fragmentation.
> Because of the defragmenter in modern operating system

### Implementation and Evaluation
- UBC data set
This data set contains scans of 857 file systems hosted on 597 computers
> windows, windows vista, windows server

## 2. Strength (Contributions of the paper)
1. this paper leverages a two-pass to filter out those chunks which only appears once.

2. The main contribution of this work is it also tracks file system fragmentation and data placement, which hash not been analyzed previously or at large scale. 
> file system data
> metadata
> data layout out

## 3. Weakness (Limitations of the paper)

## 4. Future Works  
1. This paper follows the manner that show the CDF and histogram in the same picture, which is a good to show the data distribution.