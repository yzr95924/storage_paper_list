---
typora-copy-images-to: paper_figure
typora-root-url: paper_figure
---
# Decentralized Consistent Updates in SDN

@SOSR'17  @distributed network update
[TOC]

## Summary
***Motivation of this paper***:

- 传统的centralized update需要控制器和交换机之间进行多次通信以确保满足网络一致性的依赖关系，多个round-trip delays增加了更新时延。

***Method***

1. 基本思路：采用流分段和流量分割的方法来避免拥塞出现，同时并行更新减少时延，控制器将更新前后的状态配置Conguration C and Conguration C' 下发到交换机，交换机之间通过消息交互确保执行顺序，实现分布式的数据平面流表更新。

> by obeying operation dependencies while coordinating with other switches via in-band messaging.

Update time=Computation + coordination(the interval of the first controller-to-switch is sent until when the last switch notifies the controller when update operations completed.)

computation time(≤20%)

***Implementation and Evaluation***:

1. Python, 6.5k LoC
2. Ryu controller

## Strength (Contributions of the paper)

## Weakness (Limitations of the paper)

## Related Works

## Future Works 

- 多控制器的分布式更新