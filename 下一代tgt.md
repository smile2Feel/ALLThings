下一代tgt命令
========
## client
###建连
- nvme discover -t rdma -a 172.17.29.216 -s 4420
- nvme connect -t rdma -n "nqn.2022-06.h3c.com:onestor185" -a 172.17.29.216 -s 4420
- nvme disconnect -n "nqn.2022-06.h3c.com:onestor185"

###查询
- nvme list-subsys /dev/nvme0n1(NVMe namespace device?)



## tgt端

