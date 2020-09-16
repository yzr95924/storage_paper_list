---
typora-copy-images-to: ../paper_figure
---
Side Channels in Deduplication: Trade-offs between Leakage and Efficiency 
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ASIA CCS'17 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Cross-user  client-side deduplication inherently gives the adversary access to a side-channel that may divulge whether or not a particular file is stored on the server, leading to leakage of user information.

This paper proposes formal definitions for deduplication strategies and their security in terms of adversarial advantage.
> provide a criterion for designing good strategies and then prove a bound characterizing the necessary **trade-off** between security and efficiency. 

### Method Name
Client-side deduplication is generally preferable to server-side deduplication on economic grounds. For Danny Harnik's work, it can be seen as a compromise between the efficiency of client-side deduplication and the security of server-side deduplication.
> To simplify its results, it focuses on file-based deduplication.

- The trade-off between security and efficiency 
In Danny Harnik's work, it can control the threshold to achieve the trade-off between security and efficiency. Because the threshold indicates the the maximum number of times a file may need to be uploaded and is thus the worst-case overhead for bandwidth.

- Modeling of deduplication
Here, it may wish to implement a deduplication strategy that choose the upload threshold based on some probability distribution.
> Ideally, reduce an adversary's ability to gain information from its uploads, in a way that does not severely impact the amount of bandwidth required.

In this paper, it regards **deduplication strategies** as $\rightarrow$ **distributions on the possible thresholds**.
> a strategy $DS$ can be viewed as the list ($p_0 = 0, p_1, ....$) where $p_i$ is the probability that the threshold is value $i$.
> $DS$ is a probability mass function. $DS.Alg$ is the algorithm that implements strategy DS.


- Security Notion of Existence-of-File Attack
This paper introduces the indistinguishably under existence-of-file attack (IND-EFA)
> game

Given a deduplication strategy, since the adversary's job is essentially to distinguish two probability distributions, it defines the statistical distance of the two distributions, called this **security level** $\Delta$. 
$$
\Delta =\sum^{\infty}_{i=0} |p_i - p_{i+1}|
$$



### Implementation and Evaluation

## 2. Strength (Contributions of the paper)
1. this paper proposes formal definitions for side-channel deduplication strategies, including a natural measure for effectiveness of countermeasures.
2. characterizing the trade-off between security and efficiency necessary for different strategies.

## 3. Weakness (Limitations of the paper)
1. This paper is too theoretical, and does not provide any material related to experiments.

## 4. Future Works
1. This paper shows that **uniform distribution for probabilistic uploads** provides the optimal solution for a natural measure, which presents a trade-off between security and bandwidth usages.

2. This paper also mentions the topic of security in **memory deduplication**. It says if KSM module were to use randomized thresholds for deduplication of memory pages, then the tradeoff between efficiency and security is very similar to the cloud storage scenario.