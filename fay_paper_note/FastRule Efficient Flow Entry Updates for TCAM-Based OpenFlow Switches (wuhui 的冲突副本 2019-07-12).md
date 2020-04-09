# FastRule: Efficient Flow Entry Updates for TCAM-Based OpenFlow Switches

1. TCAM物理地址从高到底，多个entries匹配时，优先选择high地址的规则执行动作

2. 改进更新的解决方案：

- 控制器层：从控制层面减少下发的flow entries[20-23]，[24-27]减少冗余的更新，Dionysus
- 数据平面层：在交换机上实现某个有效算法的固件，减少TCAM中flow entries的移动（DAG-based）  √

3. TCAM更新flow entries的两种方法：

- priority-based：插入新的规则，保证规则的优先级顺序，移动其他entries为新规则移出空间。
- DAG-based：插入新的规则，基于规则之间的依赖性构造依赖图，相当于插入一个新的node到DAG，减少移动entries   √

4. FastRule实现框架

   只要分为三个阶段：

   - DAG compiler: 把新规则的插入转换成插入一个新结点到DAG中
   - Update Scheduler：使用贪心的策略找到一个关于流flow entry的更新序列，包括(operation, match, address)的序列
   - TCAM：得到的更新序列，通过TCAM API应用到TCAM里。

5. FastRule适用于不同的TCAM layouts

   -  original layout: 规则要么在top要么在bottom，其余都是空闲的
   - interleaved layout: 规则存储的地址后留存一定的空闲容量
   - Free Spaces in the Middle： 空闲容量在中间，在top或bottom存储规则

