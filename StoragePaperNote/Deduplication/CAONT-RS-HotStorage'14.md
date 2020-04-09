---
typora-copy-images-to: paper_figure
---
Convergent Dispersal: Toward Storage-Efficient Security in a Cloud-of Clouds
------------------------------------------
|  Venue  |       Category       |
| :-----: | :------------------: |
| HotStorage'14 | dispersal, deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
The keyless security of existing dispersal algorithms relies on embedded random information which breaks data deduplication of the dispersed data. This paper proposes convergent dispersal, which replaces the original random information with deterministic cryptographic hash which is derived from the original data.

The Summary of existing dispersal algorithm
![1552893983832](paper_figure/1552893983832.png)


### Convergent Dispersal
This paper contains two convergent dispersal algorithms
> CRSSS and CAONT-RS
> both of them augment existing dispersal algorithms with the deduplication property.

- Target Deployment Scenario
Each compute server performs the cross-user deduplication.

The security of existing dispersal algorithms depends on the embedded **random information**. Due to randomness, distinct secrets with identical content lead to different sets of shares  (impede data deduplication)

- Key idea
  replace the original random information in existing dispersal algorithms with **deterministic hashes** generated from the secret.
- Convergent RSSS (CRSSS)
![1552911506553](paper_figure/1552911506553.png)
Given a secret, it splits it into a number of words which has the **same size** as hashes (e.g., SHA-256 $\rightarrow$ 32 bytes word size).
Each time, it processes $k - r$ words, and generates $r$ hashes from the $k - r$ words uising $r$ different hashes.
$$
h_i = H(D, i), for \space i = 0,1,...,r-1
$$

- Convergent AONT-RS (CAONT-RS)
  Key idea: 
  replace the random key employed in the AONT step of AONT-RS with a cryptographic hash generated from the secret. 
![1552912503121](paper_figure/1552912503121.png)
$$
c_i = d_i \oplus E(h_{key}, i), for \space i = 0, 1, ..., s-1
$$

### Implementation and Evaluation
- Investigate the throughput of CRSSS and CAONT-RS
> measure the total amount of processed secret data divided by the computational time of generating all shares.
> SHA-256: for default hash 
> AES-256: for default encryption function

- Evaluate this performance
> In local machine

Insight: CRSSS can achieve a more flexible tradeoff between security and performance than CAONT-RS.

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
This paper does not analyze how the secret size affects the deduplication ratios for different dispersal algorithms. For CRSSS and CAONT-RS, since there are some constrains on the its secret sizes. 
