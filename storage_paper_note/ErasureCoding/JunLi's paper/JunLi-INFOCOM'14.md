---
typora-copy-images-to: paper_figure
---
# Cooperative Pipelined Regeneration in Distributed Storage Systems
@INFOCOM'14 @Cooperative Regeneration
[TOC]

## Summary
***Motivation of this paper***: In regenerating codes, it needs to engage a large number of nodes during regeneration, which is not desirable in practical system. Although, some techniques have been proposed to reduce the number of participating nodes during regeneration, they either fail to maintain the recoverability property, or are not designed for regenerating **multiple data losses**. Thus, this paper proposes a cooperative pipelined regeneration process to <u>regenerate multiple data looses in batches.</u>

***Cooperative Pipelined Regeneration***:
1. Newcomers recevive data from other participating nodes and encode received data into partially regenerated coded blocks.
2. After this round of regeneration, newcomers will be partially regenerated and become **apprentices**.
3. In the next round of cooperative pipelined regeneration, apprentices and $r$ new newcomers receive data from other participating nodes with another set of $v$ providers. 
![1534941877505](paper_figure/1534941877505.png)

## Strength (Contributions of the paper)
1. This paper illustrates the pipelined regeneration with both **Random Linear Codes** and **Regenerating Codes**. 
## Weakness (Limitations of the paper)
1. The apprentices introbduce additional storage overhead in this code
2. This paper just gives the theroical analysis of this code, not includes the implementation
3. It needs to do the experiments in a practical distribution storage system. 
## Future Works
1. This work is really theorical, I think it should discuss more on how to implement it in a practical distribution system.
