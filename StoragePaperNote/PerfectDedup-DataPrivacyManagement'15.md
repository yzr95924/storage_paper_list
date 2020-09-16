---
typora-copy-images-to: ../paper_figure
---
PerfectDedup: Secure Data Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| Data Privacy Management, and Security Assurance'15 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Convergent encryption suffers from various well-known weakness including dictionary attacks

Current countermeasures: 
> popular file: CE
> unpopular file: random encryption

This paper takes into account the the popularity of data segments
> leverage the properties of Pefect Hashing in order to assure block-level deduplication and data confidentiality at the same time.

The key challenge of this popularity-based approach
> design of a secure mechanism to detect the popularity of data segments.
> how to achieve no information leakage about the popularity of popular files and unpopular files

The key motivation:
> Popular data do not require the same level of protection as unpopular data and therefore propose different forms of encryption for popular and unpopular data.

### PerfectDedup
- Main idea



### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works

