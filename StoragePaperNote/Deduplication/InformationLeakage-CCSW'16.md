---
typora-copy-images-to: paper_figure
---
On Information Leakage in Deduplication Storage Systems
------------------------------------------
|    Venue    |       Category       |
| :---------: | :------------------: |
| ACM CCSW'16 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
This paper argues that all existing proposals share the same goal that enabling cloud providers to deduplicate encrypted data stored by their users. However, they do not consider the information leakage incurred by **access traces**.
> here, this paper mainly considers the service providers

Thus, here it considers the information leaked to a curious storage provider by systems that perform **client-side deduplication and encryption**.
> storage provider can still acquire considerable information about the stored files without knowledge of the encryption key. 

### Method Name
- System Model
Client-side deduplication: the encryption key can be generated using the help of a key server.
![1558494722437](paper_figure/1558494722437.png)

- Threat Model
An adversary can acquire the access traces of storage server.
> by compromise, coercion
> access traces: entries containing a timestamp, the object ID, the object size.

The adversary wants to identify which objects forms a file.

- Storage Graph
model any file $f$ as a tree $T(f)$
> each leaf node represents an object of $f$ as output by the chunking algorithm.
> Two kinds of nodes: file nodes and object nodes
![1558513853943](paper_figure/1558513853943.png)


This bipartite graph can be built by examining at the access traces of the storage server, without the need to learn the contents of the stored files.
> anonymity set 
> candidate set: $C(f, G)$

- Storage Inference
Goal: analyze the probability that a target file $f$ is stored on $S$ given the storage graph $G$.
> File absence: $C(f, G) = \varnothing$ 
> File presence: $C(f, G)\neq\varnothing $

How to quantify the probability that $f$ is actually stored on $S$?
> take into account how likely it is for any file $f^{'} \neq f$ to have $T(f^{'}) = T(f))$
> ![1558519202625](paper_figure/1558519202625.png)

Base on **Bayesian Network**.


- Template Attacks
This kind of attack leverages the probability of storage of different files in order to infer higher-level contextual unformation.
> the adversary knows the fixed part in template and tries to infer the variable parts of the stored contracts.

- Quantifying Anonymity Sets
the anonymity set of a file $f$ is the set of all possible files that have the same deduplication fingerprint as $f$.
> 1. File-based fingerprints 
> 2. Fixed-sized block-based fingerprints
> 3. CDC-based fingerprints

**CDC-based fingerprints**
a file  of size $n$ can have a large number of possible CDC-based deduplication fingerprints. 

> this paper provides the lower bound for the number of possible deduplication fingerprints for a given file size $n$.
> Based on an modified version of the **stars-and-bars theorem**.
> Show two random files can have deduplication fingerprints collision.

### Implementation and Evaluation
1. It evaulates anonymity sets in four datasets.
> CDC-based deduplication techniques exhibit a significantly smaller anonymity sets and therefore higher leakage when compared to those of fixed-block and file-based algorithms.
> If a candidate for such a file is found

**Conclusion**
Existing public chunking algorithms allow an adversary to construct deduplication fingerprints and compare the simulated deduplication fingerprints to those on the storage.

## 2. Strength (Contributions of the paper)
1. it analyzes and quantify the information leakage to a curious storage server due the data deduplication, and shows content-based chunking methods offer clear distinguisher for stored content.
2. Do the experiment in two real world dataset, and shows that existing CDC-based deduplication schemes leak information by up to 54%.
3. It shows that fixed-sized block-based deduplication technologies establish a solid trade-off between the achieved privacy and the storage-efficiency performance.
## 3. Weakness (Limitations of the paper)
1. The theorem part is not very easy to understand. 
## 4. Future Works
1. This work leverages the **Bayesian Network** to compute the probability of a storage. And this kind of model and method can be used in the deduplication scenario.
2. This work analyzes that in CDC-based fingerprint chunk algorithm, it can have the deduplication fingerprients collision, and the adversary can exploit this to get information.