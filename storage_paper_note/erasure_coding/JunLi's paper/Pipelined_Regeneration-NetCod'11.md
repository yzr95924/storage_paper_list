---
typora-copy-images-to: paper_figure
---
# Pipelined Regeneration with Regenerating Codes for Distributed Storage Systems
@NetCod'11 @Regenerating Code
[TOC]

## Summary
***Motivation of this paper***: In regenerating codes, minimum-storage regenerating codes require the newcomer to receive $\frac{M}{k} \times \frac{d}{d-k+1}$. However, in practical distributed storage system, it is not favorable to let a large number of nodes work cooperatively. This paper proposes a pipelined regeneration process that can reduce the number of participating nodes required by regenerating codes **without sacrificing data integrity**.

***Pipelined Regeneration***:
The *independent regeneration* and *cooperative regeneration*
![1534927153524](paper_figure/1534927153524.png)
In some practical distributed storage system, the regeneration process will not be triggered until the number of failed storage node has reached a **certain threshold** and they can be regenerated in batches. (many newcomers)
![1534859524747](paper_figure/1534859524747.png)
This paper engages much fewer participating nodes by dividing one round of cooperative regeneration into several rounds of pipelined regeneration. In each round of pipelined regeneration, both the newcomer and apprentices are partially regenerated, such that there are fewer than $d$ providers. Suppose there are one newcomer, $\alpha$ apprentices, $v$ providers, during pipelined regeneration, all providers send $\alpha+1$ ($\alpha$ apprentices and one newcomer). A newcomer will be fully regenerated after $\alpha+1$ rounds of pipelined regeneration.

## Strength (Contributions of the paper)
1. This paper proposes the pipelined regeneration process, which can consume a smaller amount of traffic that would have been achieved by the conventional regeneration process with a much higher number of participating nodes.
## Weakness (Limitations of the paper)
1. It just consider the case of single failure and fails to utilize multiple newcomers cooperatively, and can only support MSR codes.
2. I feel the implementation of this code maybe very  hard. So how to combine it with a practical distributed storage system is a issue.
## Future Works
1. How to utilize multiple newcomers in cooperatice fashion, such that it can further saving the bandwidth consumption 
