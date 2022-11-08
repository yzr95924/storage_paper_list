---
typora-copy-images-to: ../paper_figure
---
Quantifying Information Leakage of Deterministic Encryption
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| CCSW'19 | Deterministic Encryption |
[TOC]

## 1. Summary
### Motivation of this paper
- Deterministic encryption
ensures that the same plaintext encrypted under the same key will produce the same ciphertext.
> enable clients to make queries on sensitive data.
> the security implications of deterministic encryption are not well understood.


- Deterministic encryption is controversial 
  - CryptoDB claims that: deterministic encryption is safe to use for sensitive field if every value in a column appears only once.
  - Deterministic encryption for non-unique fields is described as **allowing some leakage**. (there does not currently exist a clear understanding of the leakage)

This paper provides a leakage analysis of deterministic encryption through the application of the framework of **quantitative information flow**.

### Quantitative Information Flow (QIF)
- Key Insight:
there is no one "correct" way to measure leakage without a given operational scenario
> different operational scenarios require different leakage measures.

- QIF definition
  - prior g-vulnerability
  - posterior g-vulnerability
![image-20191118003954646](../paper_figure/image-20191118003954646.png)

it is natural to measure the leakage of the channel by comparing the prior g-vulnerability with the posterior g-vulnerability.

- Model of deterministic encryption
  - Bayes vulnerability
the adversary attempts to guess the entire column. consider different distributions(three kinds of values):
> 1. uniform distribution
> 2. an arbitrarily chosen non-uniform distribution
> 3. a distribution in which two values has the same probability
> 4. a distribution in which two values' probabilities are very close but not the same.

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)
1. This paper provides a comprehensive information leakage analysis via considering different distribution and different operational scenarios of the adversary.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights
1. This paper mentions the way to mitigate inference attack by inserting fake entries prior to uploading the database to the cloud.
> For encrypted deduplication, can we insert fake chunk to the original workload to mitigate the attack?

2. This paper considers different distribution and analyzes the information leakage in different cases.
> In our paper, we do not provide a fine-grain information leakage analysis, we just use KLD as a coarse measurement for information leakage.