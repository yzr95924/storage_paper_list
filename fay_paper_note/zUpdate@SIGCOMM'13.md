---
typora-copy-images-to: paper_figure
---
# zUpdate: Updating Data Center Networks with Zero Loss
@SIGCOMM'13 
[TOC]

## Summary
***Motivation of this paper*** 

Due to the massive hosted servives and uderlying physical devices, DCNs updates occur frequently, triggered by the operators, applications, or even network failures. DCN updates need not only satisfying the consistency(drop-free, congestion-free, loop-free) , but also would saving the limited switch's resource and reducing the update time, which makes DCNs update becomes difficult.

***Method***

This paper proposes a primitive, zUpdate, to perform congestion-free network-wide trffic migration during DCN updates. 

***Challenges***

- The large-scale network-wide traffic migration could lead to severe congestion.
- The state explosion problem(method:two-phase commit mechanism)

***Implementation and Evaluation***:

1. Handling update scenarios

- network topology update: switch firmware upgrade & failure repair(no traffic matrix change)
- traffic matrix updates: VM migration and LB reconfiguration

2. prototype implementation

![](paper_figure\zUpdate_prototype.png)

- When operator has an update scenario to perform, zUpdate translate the update requirement into the constraint to the target traffic distribution. 
- Then, zUpdate will automatically find a target traffic distribution, and a chain of intermediate traffic distributions which bridges the current and the target traffic distributions and makes sure that the whole transition process is congestion-free.

## Strength (Contributions of the paper)

-  propose zUpdate for a variety of update scenarios, novel algorithms to compute update plan.
-  implement the zUpdate ion commodity switches and achieve zero loss and congestion-free update.

## Weakness (Limitations of the paper)

- this paper doesn't consider reducing the update time in DCNs's network updates.

