---
typora-copy-images-to: paper_figure
typora-root-url: paper_figure
---
# RuleTris: Minimizing Rule Update Latency for TCAM-based SDN Switches

@ICDCS'16  @Rule update
[TOC]

## Summary
***Motivation of this paper***:

- 众多网络更新场景预留给更新流表的时间有限（毫秒级），但数据统计表示，基于TCAM的交换机的流表更新时延在33~400ms左右
- 交换机随机地停止处理控制平面指令时长高达400ms
- 控制平面优化了处理时延，流表更新成为网络更新pipline的瓶颈
- 已有的工作都没有考虑到物理交换机上单条流表的更新时延

> Interestingly, although a single entry move in TCAM usually has a constant sub-millisecond delay, we observe that an OpenFlow rule update sometimes triggers hundreds to thousands of unnecessary entry moves in TCAM to maintain rule dependency due to its unawareness of the minimum dependency information.

***Method***

1. 基本思路：基于优先级的流表依赖关系存在冗余，导致在TCAM-based交换机上的单条流表更新引起大量的entry移动，造成更新时延加重。RuleTris提出将流表间依赖关系转换成依赖图，可以消除冗余流表依赖，达到最小依赖关系集并确保优化的更新策略。利用集成在控制器上的Front-end得到DAG和流表（流表增量的更新），下发到数据平面的Back-end，消除冗余依赖，生成TCAM entry移动序列。
2. 简单的TCAM交换机更新一条流表的移动次数和RuleTris的优化例子

![](/RuleTris_simple_example.png)

2. 可消除两种类型的冗余规则：模糊规则和浮动规则

> Through a simple topological scanning, RuleTris can eliminate two types of redundant rules generated during modular composition, i.e., the rules obscured by higher priority rules (or obscured rules) and the rule having the same actions with lower priority but more general rules (or floating rules).

- 模糊规则

​      规则A优先级高于规则B，B永远不会匹配到数据包，B冗余。

- 浮动规则

​      规则A和规则B毗邻（优先级A>B），执行的动作相同，B的match域宽于A，则A是floating规则，冗余。

3. 模型

- 流程：控制器实现front-end，组合编译器根据算法得到规则依赖关系图和流表（消除匹配模糊），下发到数据层，数据层依次完成冗余规则的删除和更新调度，并得到TCAM交换机的流表entry移动，完成流表在物理交换机的更新。
- 通信：扩展了支持携带DAG或更新DAG的OpenFlow协议

![](/RuleTris_prototype.png)



***Implementation and Evaluation***:

1. Front-end composition compiler,
   - 5K lines of Java code.

2. Back-end optimizers
   - extending the firmware on the ONetSwitch
   - 3K lines of C code.

## Strength (Contributions of the paper)

1. 提出了一种依赖保留算法，保证产生最小DAG

> The algorithms achieve efficiency by exploiting the dependency implications of composition operators. The algorithms are generic to SDN policy languages that employ policy compositions (sequential, parallel and priority), and are guaranteed to produce the minimum DAG.

2. 完成对DAG的增量式更新，将DAG转换成TCAM流表更新，消除冗余规则，减少更新时间

## Weakness (Limitations of the paper)

更新时间虽然减少了，但是交换机固件计算基于DAG的策略流表更新比基于优先级的方法所花时间更多。

## Related Works

1. Dionysus

> significantly reduces multiswitch policy update latency caused by suboptimal scheduling.

2. CoVisor and  Compiling Minimum Incremental Update for Modular SDN Languages.

> minimize the number of rule updates sent to switches through eliminating redundant updates.

## Future Works 

