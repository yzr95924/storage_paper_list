# Towards Rule Enforcement Veriﬁcation for Software Deﬁned Networks

REV@infocomm'17

## Summary

### motivation of this paper

- 攻击者可能利用交换机操作系统的漏洞、攻击控制信道，修改控制器下发的规则，改变数据包原来的转发逻辑，违背网络策略。为了避免此类攻击，验证数据平面规则执行的正确性，提出了一种基于压缩的MAC（消息认证码，带密钥的哈希函数）方法。

- 规则篡改攻击的检查困难性，SDN模型的开环控制使得检测十分不易，控制器只负责将规则安装在交换机，但不能验证这些规则是否被交换机正确地执行了。

- 已有的工具在交换机不可信情况下，不能验证正确性。

### Method

- 数据包进入网络后，入口交换机发送数据包哈希值给控制器，并分配一个tag给它，每个下游交换机根据控制器共享的密钥更新tag，直到出口交换机将数据包发送给控制器。控制器验证数据包tag，可以得知数据包是否有按照下发的规则执行转发。

- 为了减少每个包都要在入/出口交换机向控制器发送数据包带来的开销，对流(flow)级的数据进行验证，即将每条流压缩成一个flow级数据包，在流结束的时候发送到控制器执行验证。

网络模型对flow的定义：包含相同headers的所有数据包集合，例如属于同一个TCP会话的数据包。

#### Packet-level REV

1. 初始化

   每个会话验证一个包的规则执行。

2. tagging

3. 验证

#### Flow-level REV

1. 初始化

   控制器下发包含VID，Inport等的消息，并创建source flow-packet和destination flow-packet，值都初始化为0。

2. tagging：伪随机数生成器。

   例子（入口交换机下一跳S1）：根据控制器共享的K1，VID使用伪随机函数生成SK1,a和SK1，b两个密钥，再利用PRNG生成向量α1，点乘数据包pi得到a，再利用第二个密钥SK1,b，VID||S0||i作为伪随机函数输入生成b，得到第一跳交换机上的第i个数据包的tag。
   $$
   t_{i,1} \leftarrow a+b \in F_q
   $$
   
3. 压缩

   源交换机在发送第i个数据包pi的时候，生成一个密钥并更新source flow-packet的值；目的交换机每收到pi（长度此时为l+n），类似地也生成一个密钥，然后用于更新destination flow-packet内容。

4. 验证

   输入：交换机Si的私钥Ki，规则集R，验证会话ID(VID)，对应VID的流f，f的包数量，源和目的flow-packet

   输出：true或false

   首先计算流f的转发路径，如果源flow-packet是否和截断目的flow-packet到长度为l之后的数据包是否一样，或者目的流包的长度不等于(n+l)，直接返回false。

   然后对i从1到n(核心交换机个数为n)，计算SKi,a和SKi,b，然后生成α向量，依次得到每一跳的a+b，判断（a+b）和目的流包的第(l+i)位相等，如果所有位都相等，则返回True。

### Implementation

**REV头部**

介于IP和TCP/UDP头部之间，包含VID, TimeStamp, PktHash，总长度固定为16位。此外还包含了tag的t字节的可变长度，随着遍历转发路径上的交换机，生成的tag。（目前的版本还没有包括签名）

**加密操作**

伪随机数生成器G：AES-128，计算器模式；

伪随机函数F：AES-128，CBC(密码分组链接)模式；

加密的哈希函数H：SHA1；

SHA1和AES都是使用OpenSSL实现的，有限域上的计算使用快速有限域算法库。

**REV服务器**

- 运行在RYU控制器上，维护了一个路径表path table，用于记录端到端的所有路径，使用入口交换机ID和端口号及包头作为查找索引，路径表的构造参考VeriDP[1]。
- 公开了REST API允许用户发出验证查询，例如给定源ip和目的ip，batch大小，curl提交到服务器。
- 服务器收到查询请求，查找路径表找到flow对应的路径，并建立一个验证的会话session。发送验证流的信息和batch大小到入/出口交换机。
- 服务器和交换机之间通过OpenFlow的experimenter消息交互。

**REV**数据平面

基于CPqD openflow交换机实现。增加了两个模块：

- 与服务器通信experimenter消息的REV代理，建立对称密钥，初始化验证会话，发送压缩的包/tags
- REV包处理模块，负责插入、移除、解析REV头，计算、压缩和发送MACs

入口交换机维护一张包括所有活动的验证会话，如果一个数据包属于一个验证会话，源(入口)交换机插入一个REV头到数据包，所有下游交换机只需要验证是否有REV头以确定是否要为该数据包生成MAC；当验证流的包计数到达batch大小，或者会话时间超时，源和目的交换机发送各自的flow-packets到REV服务器。

## Strength

1. 提出规则执行验证(REV)，用来防御SDN网络中的规则修改攻击，是第一个在SDN可攻击场景下提出的规则验证研究。
2. 实现了一种安全的压缩MAC方法
3. 验证REV的性能，减少了97%的控制信道流量开销，相较于标准MAC的吞吐量提升了8倍。

**注意**：每次验证不同的流，控制器都会下发不同的会话密钥SKi。流是基于相同的TCP五元组来定义的。

## Future Works

[1] Stick to the script: Monitoring the policy compliance of SDN data plane, ANCS 2016