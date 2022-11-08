---
typora-copy-images-to: paper_figure
---
# RAFI: Risk-Aware Failure Identification to Improve the RAS in Erasure-coded Data Centers

@ATC'18 @Failure Identification
[TOC]

## Summary
***Motivation of this paper***: The recovery phase of data repair is widely studies and well optimized, In traditional failure identification scheme, all chunks are share the same identification time threshlod, thus losing the opportunities to further improve the RAS. This paper regards data repair as two phases: **recovery phase** and **identification phase**. And it focuses on the **identification phase**.
> The RAS cannot be improved simultaneously by adjusting the failure identification time threshold.

***RAFI***

- **Key principle of RAFI**: This is based on the dedicated observation on stripes, and it can be classified into two types:
> 1. a stripe has many failed chunks (high risk stripe) $\rightarrow$ a shorter identification time threshold 
> 2. a stripe has a few failed chunks (low risk stripe) $\rightarrow$ a longer identification time threshold

For the stripes having many failed chunks, it tunes down the failure identification time threshold of those failed chunks (improve the data availability and reliability $\rightarrow$ increasing the repair network traffic)

For the stripes having a few failed chunks, it tunes up the failure identification time threshold of those failed chunks (reducing the repair network traffic $\rightarrow$ reducing data reliability and availability)

![1536304821166](paper_figure/1536304821166.png)

- The time identification threshold is determined by the **total failed chunks** in the stripes (risk level). 

- **Compatibility**: Due to the fact that RAFI focuses on the failure indentification phase, it can work together with existing optimizations which focus on the failure recovery phase.

***Implementation and Evaluation***:
**Implementation**:
It proposes a hybrid methodology to comprehensively evaluate RAFI via both **simulation** and **prototype implementation**.
>1. the design details and computational cost of RAFI $\rightarrow$ real distributed storage system
>2. the effectiveness and efficiency of RAFI on the RAS $\rightarrow$ Monte-Carlo simulation.

![1536311837927](paper_figure/1536311837927.png)

**Simulator**:
This paper also developed a simulator, which is written in R (open-source).
![1536314355540](paper_figure/1536314355540.png)
This paper does 1) Functions of Erasure Coding Schemes 2) Functions of Recovery Network Bandwidth 3) Comparisons with *Lazy* all in hte simulator.
## Strength (Contributions of the paper)
1. It proposes a risk-aware failure identification scheme RAFI to simultaneously improve the Reliability and Availability and Serviceability (RAS).
2. It also designs a simulator to verify the RAFI.
3. It implements the prototype of RAFI in HDFS to verify the correctness and computational cost of it.
## Weakness (Limitations of the paper)
1. It postpones the failure identification of chunks in low risk stripes, more failed chunks might be generated, thus increasing degraded reads.
2. RAFI needs to collect the trace firstly and then do the calculation to get the time threshold, which can be out-of-date.
3. This paper does not mention how it metric the **reliability, availability, serviceability**.

## Future Works
1. I think this paper can further investigate how to do the tradeoff between failure identification time and the number of degraded reads happen, corresponding to the first weakness.