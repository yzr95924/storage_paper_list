---
typora-copy-images-to: paper_figure
---
 Information Leakage in Encrypted Deduplication via Frequency Analysis
------------------------------------------
| Venue  |       Category       |
| :----: | :------------------: |
| DSN'17 | Secure Deduplication |
[TOC]

## 1. Background and Motivation
- Encrypted deduplication seamlessly combines encryption and deduplication to simultaneously achieve both **data security** and **storage efficiency**.
- The deterministic encryption inherently reveals the underlying <u>frequency distribution</u> of the original plaintext chunks (an adversary can launch frequency analysis and infer the content of the original plaintext chunks) 产生information leakage!
- Conventional (symmetric) encryption is **incompatible** with deduplication. (基本都会说的老套路)
- Existing MLE implementations still cannot fully protect against content leakage, mainly because their encryption approaches are **deterministic** and an adversary can analyze the <u>frequency distribution</u> of ciphertext chunks and infer the original plaintext chunks based on classical frequency analysis.
  > In practical storage workloads often exhibit **non-uniform** frequency distributions in terms of the occurrences of chunks with the same content.
  > ![1522721106458](paper_figure/1522721106458.png)

- A sinplest form of the attack:

  > 1. an adversary first obtains **prior knowledge** of frequency distributions of plaintext chunks.
  > 2. count the frequencies of all the ciphertext chunks
  > 3. infer their corresponding plaintext chunks based on the frequency distribution of ciphertext chunks.
  >

- The *practical* implications of frequency analysis against encrypted deduplication remain **unexplored**.

- **Chunk locality**: If a plaintext chunk $M$ corresponds to a ciphertext chunk $C$, then the neighboring plaintext chunks of $M$ are likely to correspond to the neighboring ciphertext chunks of $C$.

- **Deduplication**: it is a coarse-grained compression technique to save storage space.
  > 1. After chunking, each chunk is identified by a *fingerprint*, which is computed from the cryptographic hash of the content of the chunk.
  > 2. Two non-identical chunks have the same fingerprint is practically **negligible**. 
  > 3. To check if any identical chunk exists, the storage system maintians a *fingerprint index* (a **key-value** store that holds the mappings of all fingerprints to the address of physical chunks that are currently stored.)
  > 4. For each file, the storage system also stores a **file recipe** that lists the references to all chunks of the file for future reconstruction.

- MLE can only achieve security for **unpredictable chunks**, meaning that the size of the set of chunks is **sufficiently large**, such that the brute-force attack becomes infeasible.

##2. Threat Model
- It focus on **backup workloads**, which have substantial content redundancy and are proven to be effective for deduplication in practice.
- It assumes the adversary is **honest-but-curious**
  > 1. means it does not change the prescribed protocols for the storage system and modify any data in storage.
  > 2. To launch frequency analysis, the adversary should have access to *auxiliary information* that provides ground truths about the backups being stored.
  > 3. **auxiliary information**: the plaintext chunks of a prior (non-latest) backup, which may be obtained through **unintended data releases** or **data breaches**.
  > 4. The **primary goal** of the adversary is to *infer* the content of the plaintext content of the plaintext chunks that are mapped to the ciphertext chunks of the latest backup.
  >

- Two attack modes:
  > 1. *Ciphertext-only mode*: the adversary can access the ciphertext of the latest backup.
  > 2. *Known-plaintext mode*: a **powerful** adversary can not only access the ciphertext chunks of the latest backup but also knows <u>a small fraction of the ciphertext-plaintext chunk pairs about the latest backup.</u>
  >   Those two attack modes can also access the **logical order** of ciphertext chunks of the latest backup before deduplication.
  >

## 3. Attack
- It presents **inference attack** based on frequency analysis against encrypted deduplication.
- The adversarial goal of the attack: 
  > 1. Let $C = <C_1, C_2, ...>$ be the sequence of **ciphertext** chunks in logical order for the latest backup.
  > 2. Let $M=<M_1, M_2, ...>$ be the sequence of **plaintext** chunks in logical order for a prior backup. 
  > 3. Note that both $C$ and $M$ do not necessarily have the **same number** of chunks.
  > 4. **Goal**: Given $C$ and $M$, an adversary aims to infer the content of the original plaintext chunks in $C$.

### 3.1 Basic Attack
- In basic attack, it firstly identifies each chunk by its fingerprint and **counts the frequency** of each chnk by the number of fingerprints that appear in a buckup.

  > a chunk has a high frequency if there exist many identical chunks with the same content (因为假设adversary是在参考deduplication之前的数据)

- It secondly **sorts** the chunks of both $C$ and $M$ by their frquencies
- Thirdly, it **infers** the $i-th$ frequent plaintext chunk in $M$ is the original plaintext chunk of the $i-th$ frequent ciphertext chunk in $C$. (因为是deterministic encryption的原因)

- Algorithm~1 (**basic attack**):
  > 1. *input*: $C$ and $M$
  > 2. *return*: set $\Gamma$ of ciphertext-plaintext chunk **pairs**
  > 3. *FREQ-ANALYSIS* performs frequency analysis based on $F_C$ and $F_M$. Since $F_C$ and $F_M$ may not have the same number of elements, it finds **the minimum number** of elements in $F_C$ and $F_M$. Finally, it returns the ciphertext-plaintext chunk pairs, in which both the ciphertext and plaintext chunks of each pair have the **same rank**.

- **Discussion**: The inference accuracy of *Basic Attack* is small because 
  > 1. Basic Attack is sensitive to **data updates**, an update to a chunk can change the frequency ranks of multiple chunks, <u>including the chunk itself and other chnks with similar frequencies</u>.
  > 2. There exist many **ties**, in which chunks have the same frequency. How to break a tie during sorting also affects the frequency rank and hence the inference the accuracy of the tied chunk.
  >

### 3.2 Locality-based Attack

- *Locality-based Attack* exploits **chunk locality** to improve the severity of frequency analysis.
- In their observation: if a plaintext chunk $M$ of a prior backup has been identified as the original plaintext chunk of a ciphertext chunk $C$ of the latest backup, then **the left and right neighbors of $M$ are also likely to be original plaintext chunks of the left and right neighbors of $C$.**

  > Because <u>chunk locality implies that the ordering of chunks is likely to be **preserved** across backups</u>

- Why this attack has more severity?

  > 1. For any inferred ciphertext-plaintext chunk pair $(C,M)$, we further infer **more** ciphertext-plaintext chunk pairs through the <u>left and right neighboring chunks of $C$ and $M$, and repeat the same inference on those newly inferred chunk pairs</u> (通过这样的方式增加**attack severity**)

- The locality-baed attack proceeds as follows: in **each iteration**
  > 1. it picks one ciphertext-plaintext chunk pair $(C, M)$ from $\xi$
  > 2. it collects the corresponding sets of neighboring chunks $L_C, L_M, R_C$ and $R_M$.
  > 3. it applys frequency analysis to find the most frequent ciphertext-plaintext chunk pairs from each of $L_C$ and $L_M$, and similarly from $R_C$ and $R_M$.
  >   There are three factors need to configure:
  > 4. $v$: indicate the number of most frequent chunks pairs returned from frequent analysis in an iteration.
  > 5. $u$: indicate the number of most frequent ciphertext-plaintext chunk pairs to be returned.
  > 6. $w$: bound the maximum size of $\xi$.

- The locality-based attack can successfully infer the original plaintext chunks of all ciphertext chunks. It **cannot** infer the original plaintext chunk that does not appear in $M$.

## 4. Attack Evaluation
- Implement both the basic and locality-based attacks in **C++**.
- Associative array: as key-value stores using **LevelDB**.
- Dataset
  > **FSL** is a real-world dataset collected by the File systems and Storage Lab (FSL).
  > Synthetic: an initial snapshot from Ubuntu 14.04 virtual disk image.

- Quantify the severity of an attack using the **inference rate %**.
- Impact of parameters
- Inference rate in ciphertext-only mode
- Inference rate in known-plaintext mode

## 5. Defense
- To defend agsinst frequency analysis, it considers a **MinHash Encryption Scheme**

  > Encrypts each copy of identifal plaintext chunks into **possibly different ciphertext chunks**, so as to <u>hide the frequency distribution</u> of original chunks.

- MinHash encryption builds on **Broder's theorem**:
  > if two sets share a large fraction of common elements (i.e., they are highly similar), then the probability that both sets share the same minimum hash element is also high.
  > relax the deterministic nature of encrypted deduplication by **encrypting some identical plaintext chunks to multiple distinct ciphertext chunks.** 

## 6. Defense Evaluation
- Robustness Against Leakage
- Storage Efficiency