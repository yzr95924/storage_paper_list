## Providing Reliable FIB Update Acknowledgments in SDN

@CoNEXT 2014 

[TOC]

### Summary

*motivation of this paper*

OF协议没有确认规则安装和修改的机制，OpenFlow支持的Barrier Request/Reply消息机制的缺陷性，不能严格确认数据平面规则安装的时间，甚至会提前290ms发送reply到控制器。这一缺陷可能违反安全策咯，破坏带宽限制或者黑洞环路等。

<u>”Unfortunately, in OpenFlow, currently the most popular SDN protocol, there is no mechanism with a sole purpose of acknowledging rule modications. Instead, there exists a Barrier command with a more general functionality.“</u>



*method*

(Rule Update Monitoring) 依赖于控制和数据平面测量创建一个透明层，确认数据平面规则安装。

<u>“Our key insight is in creating a transparent layer that relies on control and data plane measurements to conrm rule updates only when the rule is visible in the data plane.”</u>

- Acknowledging rule modifications
- Providing reliable barriers

实现RUM模型，以TCP代理的形式工作在交换机和控制器之间。

- POX controller

### Contributions of the paper

### Limitations of the paper

### Future works

### Related to my paper

