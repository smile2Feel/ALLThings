##tgt源码
###tgt-offical
- SCSI state machine, I/O execution
- challenges: Block Layer and SCSI Layer designs(where requests initiated.)
- SCSI-3(to enable SAN)[interface, protocol, device model, command set are independent]
- Linux kernel initiator support(FC, TCP/IP, RDMA, SPI), however no mainline target support.
- **target driver** (怎么样被使用的？怎么理解是否需要一个**specific driver**?)
- **structure of Target Framework(tgt)**
 -  **user space**
	- tgtadm
	- tgtd(SCSI state machine:execute commands and task management requests)
	- transport library(equivalent to a kernel transport class)
	- Target driver libraries(special setup or tear down)
 - **kernel space**
 	- target driver(manage the transport connections with initiator devices, pass commands and task management function requests between its hardware or interconnect subsystem and tgt-core)
 	- tgt core(connector btw target drivers and the tgtd, enables the driver to send tgtd a vector of commands or task managements function requesets throught a **netlink interface**)
 	- **Tgt core was integrated into scsi-ml**