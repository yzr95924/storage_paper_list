---
typora-copy-images-to: ../paper_figure
---
Privacy at Scale: Local Differential Privacy in Practice
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SIGMOD'18 | Local Differential Privacy |
[TOC]

## 1. Summary
### Motivation of this paper
This paper shows an introducation to local differential privacy. It descirbes three practical realizations of LDP algorithms for **collecting popularity statistics**.
1. RAPPOR from google: combines Randomized Response with Bloom Filters to compactly encode massive sets (CCS'14) 

2. Apple differential privacy: using Fourier transform to spread out signal information, and sketching techniques to reduce the dimensionality of the massive domain.
> identifying differentially private heavy hitters 
> NIPS'17, STOC'15, arXiv'17
 
3. Microsoft Telemetry collection: make use of histograms and fixed random numbers to collect data over time (NIPS'17)


- LDP and DP
1. DP
In the standard (or centralized) setting, each user sends **raw data** $v$ to the aggregator, who obtains the true distribution, **add noise**, then publishes the result.
> In this setting, the aggregator is trusted to not reveal the raw data and is trusted to handle the raw data correctly.

2. LDP
In this setting, each user perturbs the data **locally**, and thus does not have to trust the aggregator. LDP has a **stronger** privacy model than DP
> however, entails greater noise.


## 2. Future Works
