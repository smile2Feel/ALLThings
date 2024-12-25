RDMA_CM_EVENT_DEVICE_REMOVAL 事件处理
===========================
测试调研
----------
- 测试mellaox驱动5.3和5.6在网卡断开时发送了什么事件（简单程序测试），并了解原因
- 测试下一代tgt和5.3环境下断网卡报错信息
- 固件配置可以设置该事件的触发吗？试验下！
> 不行，触发该事件环境该配置也是一样的


## mlnx5.3出现该事件
*跟bond模式有关*
[**bond**模式创建](https://support.mellanox.com/s/article/howto-create-linux-bond--lag--interface-over-infiniband-network)
[**LAG**](https://www.spinics.net/lists/netdev/msg572916.html)

tgt启动时bond模式相关处理：
**启动的三个阶段(rdma device相关)**
1. **lld_init->iser_init** driver初始化,iser初始化（iser_add_portal:rdma_create_id,rdma_bind_addr,rdma_listen)
2. **iscsi_tcp_find_interface** 配置恢复阶段（iscsi_add_portal:socket,bind,listen)
3. **RDMA_CM** 连接事件管理（rdma_get_cm_event, ibv_get_cq_event)

bond 网卡启动流程：
1. ip层启动（第二阶段检测到ip）
2. rdma device启动（第一阶段rdma_create_id能检测到） -->如何确保rdma_create_id时bond口已经就绪？
 - **研究rdma_create_id**源码（ibv_get_device_list - Get list of IB devices currently available, available是什么意思？和ip层是否就绪有关吗？网卡down掉会影响吗？ibstatus有字段对应码？show_gids必须有gid？为何非bond网卡无此问题？）
 - 
3. 完全启动

tc qdisc del dev ethB86-1 root netem delay 1000ms 