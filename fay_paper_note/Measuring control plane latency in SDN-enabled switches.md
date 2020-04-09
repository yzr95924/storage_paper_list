周报10     吴慧

本周工作

1. 看了测switch时延的一篇文章，里面讨论了不同因素（比如流表的优先级、流表数量、操作类型等）对sdn switch流表更新时延的影响，当流表数量不同时会对流表的插入时延造成很大影响；
2. 改了部分ppt的思路，整理论文资料并思考如何测量数据平面时延；

下周计划

1. 把这周整理出的论文看1~2篇；
2. 测时延常用RYU控制器来实现，所以下周想先跑一下RYU；

## Measuring control plane latency in SDN-enabled switches

@sosr'15   

Latency between controller and switch:

1.  inbound latency: the latency involved in the switch generating events(e.g. packet-in messages), 8ms per packet on average on Intel.
2. outbound latency: installing/modifying/deleting forwarding rules, 3 ms per rule for insertion and 30 ms per packet for modification.

Two key factors to outbound latency:

- table occupancy

- table priority

Operation's latency

1. insertion: 3.12 ms per rule

- 下图表示改变插入规则的数量，分析比较在优先级的影响下流表规则插入的时延结果
- 图a表示当switch中已有high priority rules为100或400条时,插入low priority rules不同时，两类情况都是和流表数量成线性增长关系
- 图b表示当switch中已有low priority rules为100或400条时,插入low priority rules不同时，差异巨大的更新时延

![](./paper_figure/InsertionLatency.png)

2. modification: 

- 2X higher than insertion

- The per-rule modification delay is independent of rule priority, table occupancy, and burst size.

3. deletion:

- 删除时延随着删除规则的数量的增加而减少
- 不受优先级影响
- Allowing rules time out rather than explicitly deleting them, if possible. 因为规则time out的处理不会影响flow_mod，所以在允许的情况下可以选择不删规则而是使其timeout.