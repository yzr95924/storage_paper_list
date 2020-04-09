# cacheFlow

@SOSR 2016

cacheFlow考虑到用商业交换机来作为cache存储规则，具有两个特点：

1. 对于大规模流表错误处理

   如果tcam存储满了，剩下的将被存储在软件流表空间。

2. 完成控制命令处理慢

   tcam交换机在更新和查询流量统计信息时响应很慢。更新所需要的时延和被增加和删除的规则数量之间是一个非线性函数；在更新期间如果有查询请求，会轻易使得交换机cpu利用率到100%，更严重会导致交换机和控制器断开连接。

   这篇文章通过等待规则安装完毕后，再定期询问计数器，也借助软件交换机的计数统计。





网络策略更新的目标：fast and consistent dynamic policies update 



# TCAMs and OpenFlow – What Every SDN Practitioner Must Know

引址：<https://www.sdxcentral.com/articles/contributed/sdn-openflow-tcam-need-to-know/2012/07/>