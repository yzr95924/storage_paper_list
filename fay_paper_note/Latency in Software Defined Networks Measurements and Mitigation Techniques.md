# Latency in Software Defined Networks: Measurements and Mitigation Techniques

@[SIGMETRICS '15]

和Measuring Control Plane Latency in SDN-enabled Switches@[SOSR'15]是同一作者，内容相差不大

## SDN控制与数据平面时延

- inbound latency

  - 交换机生成packet event到控制器
  - 时延主要由ASIC和switch cpu之间的Bus带宽影响而产生，取决于packet_in率

- outbound latency

  - 接收控制器下发操作，完成insert/modify/delete rules的时延

  - 考虑的时延影响因素

    - flow table occupancy
    - rule priority

  - 插入时延结论

    - 规则复杂程度不影响插入时延

    - IBM/Intel交换机同一优先级的插入很快，不受流表占用的影响

    - 优先级的插入模式对时延的影响很大，且不同

      - Intel：递增优先级和相同优先级时相同，但是递减时有更大的时延
      - BCM-1.3和IBM的趋势和Intel相反
      - BCM-1.0递增递减的情况都比同优先级要高

    - 造成不同switch时延差距的根本原因

      - 硬件影响

      1. TCAM中规则的组织形式
      2. TCAM slices的数量

      - 交换机软件负载

  - 修改时延

    - BCM-1.0和IBM的修改时延只受流表占用率的影响（未优化软件）
    - BCM-1.3的修改时延和流表占用和优先级都没有关系（比插入时延高2x）

  - 删除时延

    - 三种交换机的删除时延都不受流表中已有规则优先级或删除顺序的影响
    - 受流表占用的影响，随着交换机已有规则的数量减少而减少（作者推测删除操作可能会引起TCAM重排）