---
typora-copy-images-to: paper_figure
---
Information Leakage in Encrypted Deduplication via Frequency Analysis
------------------------------------------
| Venue  |       Category       |
| :----: | :------------------: |
| DSN'17 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Existing MLE implementations still cannot fully protect against content leakage, mainly because their encryption approaches are deterministic.
Previous stduies have addressed the possibility of launching frequency analysis MLE-based storage and also proposed cryptographic mechanisms to mitigate the issue.
> Those investigations are theoretically driven.

### Locality Based Attack
- Chunk Locality
This is very prevalent in backup workloads that chunks are likely to re-occur together with their neighboring chunks across backups.
> rationale: changes to backups often appear in few clustered regions of chunks.

In security perspective: if a plaintext chunk $M$ corresponds to a ciphertext chunk $C$, then the neighboring plaintext chunks of $M$ are likely to correspond to the neighboring ciphertext chunks of $C$.

- Threat Model
An attacker may obtain the auxiliary information (as the plaintext chunks of a prior backup).
> Ciphertext-only mode: can only access the ciphertext chunks
> Known-plaintext mode: a more powerful adversary that knows a small fraction of the ciphertext-plaintext chunks pairs about the latest backup.


- Attack Model
Message, ciphertext model:
> $M_1, M_2, ....$, $C_1, C_2, ...$ shows the logical order for message chunks and ciphertext chunks.

**Basic Attack**:
It takes in $C$ and $M$ as input, and returns the result set of all inferred ciphertext-plaintext chunk pairs. 
> just counting the frequency of each kind of chunks, and sort them to generate ranking, and find the message and ciphertext chunks with the same ranking.

This kind of attack is sensitive to data updates that occur across different versions of backups over time.
> it exists many ties, i.e., chunks have the same frequency. How to break ties during sorting also affects the frequency rank.

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
