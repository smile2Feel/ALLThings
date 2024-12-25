[markdown-Cheatsheet-here](https://github.com/adam-p/markdown-here/wiki/Markdown-Here-Cheatsheet)

常用linux命令
===========

磁盘相关
-----------
- du -h --max-depth=1 /var/log/storage/TGT/ 显示目录或文件大小
- df -T 查看已经挂载的分区和文件系统类型
- fdisk -l 显示出所有挂载和未挂载的分区，但不显示文件系统类型
- parted -l  查看未挂载的文件系统类型，以及哪些分区尚未格式化
- lsblk -f  查看未挂载的文件系统类型
- linux磁盘挂载步骤：
 - fdisk -l 查看磁盘信息
 - fdisk -u /dev/vdb 创建分区
 - fdisk -lu /dev/vdb 查看新分区信息
 - mkfs -t xfs /dev/vdb1 创建文件系统
 - mkdir -p /mnt 创建挂载路径
 - echo `blkid /dev/vdc1 | awk '{print $2}' | sed 's/\"//g'` /mnt xfs defaults 0 0 >> /etc/fstab 修改/etc/fstab文件并挂载分区
 - cat /etc/fstab 查看新分区信息
 - mount /dev/vdb1 /mnt 挂载分区
 - df -h 检查挂载结果(查看磁盘空间占用情况)

杂七杂八
-----
- man errno; errno -l 查看ERRNO相关
- dmesg, /var/log/messages  [区别](https://unix.stackexchange.com/questions/35851/whats-the-difference-of-dmesg-output-and-var-log-messages)
- shell命令行循环执行
``` bash
mycount=0; while (($mycount < 10));do <command>;((mycount = $mycount+1));done;
```
- cat /proc/[pid]/status 查看进程资源使用，RSS为常驻内存
- ps aux | sort -k4,4nr | head -n 10  查看内存占用前10名的程序
- tar -zcvf /home/xahot.tar.gz /xahot 把/xahot文件夹打包后生成一个/home/xahot.tar.gz的文件
- 云上：ftp://10.132.193.116， 云下：10.132.206.121

日志查看 [炒鸡详细的VIM搜索替换](https://tanqisen.github.io/blog/2013/01/13/vim-search-replace-regex/)
------
- linux 统计某个字符串出现次数
 - :%s/objStr//gn （VIM)
 - grep -o 'objStr1\|objStr2'  filename|wc -l  #直接用\| 链接起来即可
- :[range]s/from/to/[flags] 简单替换表达式
 - :1,10s(1到10行) :10s（第十行）:%s（全局）
 - flags: c confirm, e error, g global（整行）, i ignore(大小写)



更新安装tgt
----------
- rm -rf /var/log/storage/TGT/
- tar -xmf tgt_iser_new_1.0.65.1.tar.bz2
- cd /home/SDS_Admin/tgt/
- ./install_tgt.sh
- service tgt forcedrestart
 - log_set_level -m TGT -l **debug**

更新内核
-----
首先uname -a查看内核版本
1. 把主线的产品包解压之后进入 /product/kernel
2. 执行 rpm -ivh --force xxxxx  #xxx表示内核版本

tgt相关日志路径
----------------
- /var/log/storage/TGT/
- /var/log/storage/backup/TGT/
- /var/log/storage/*BLKLEADER*/
- /var/log/storage/LEADER/
- /var/log/storage/PEON/
- /var/log/_messages_

tgt相关命令
--------------
tgtadm *-m __system__* *-o __show__*
tgtadm -m target -o show
tgtadm -m system *-o __update__* -n **perf** -v __on__
tgtadm -m system -o stat
iscsiadm -m discovery -t st -p 172.17.134.84 -I iser -l
tgtadm -m session -o show
iscsiadm -m session
iscsiadm -m node -T iqn.2018-01.com.h3c.onestor:66c1050f20894cdd9bebe32bfe198ff9 -p 172.17.136.131 -u(单个节点)
tgtadm  -m system -o update --name=validate --value=on 开启crc
####alua 相关命令
1. tgtadm -m initiator -o new --initiator-id 23 --initiator-name abcdefff --alua-type local --alua-model unknown
2. tgtadm -m initiator -o show --initiator-id 23
3. tgtadm -m portal -o new --port-id 95 --port-ipv4 182.200.136.154 --port-tpg 0 --port-station stationA
4. tgtadm -m portal -o show
5. tgtadm -m portal -o look --port-id 95

切换为iser | 
--- |
vim /etc/ceph/ceph.conf |
supervisorctl restart all |
service tgt forcedrestart |
service crond stop 挂掉自动拉起服务
kill -9 [process_detect]
tgtadm --op update --mode sys --name State -v offline tgt设置为offline模式，不接受任何连接
tgtadm  -m system -o update --name=validate --value=on  开启crc日志，长度偏移

iscsiadm 相关命令
-------
[全套服务](https://www.cnblogs.com/qeelee/p/5159143.html)
- iscsiadm -m discovery -t sendtargets -p 10.133.6.83:3260 扫描某个portal上的所有target
- iscsiadm -m node  显示initiator上所有已知的target
- iscsiadm -m node -T iqn.2015-12.com.example:maqi-first-target --logout/login 登入登出某个Target
- iscsiadm -m node -T iqn.2015-12.com.example:maqi-first-target -p 10.133.6.83:3260 -o show  显示某个target的细节
- iscsiadm -m session --rescan 重新扫描所有session，更新卷（删除卷无效）
- iscsiadm -m session -r SID --rescan 重新扫描指定session （删除卷无效）
- If you want something that adds and deletes luns then use the **rescan-bus.sh** script in the sg utils.

抓包
-----------
1. tcpdump -i mlx5_0 dst host 172.17.134.82 -nn -vvv -w UIS_login.pcap
 - tcpdump -i mlx5_0 vrrp

git使用
----
- git remote update origin --prune 更新远程分支
- git push origin UniStorOS_V100R001B01:refs/for/UniStorOS_V100R001B01 提交本地分支
- git show commitid/ commitid filename 查看某次commit的修改内容
- git修改commit信息
 - git rebase -i 列出commit 列表
 - 找到需要修改的commit 记录，把 pick 修改为 edit 或 e ， :wq 保存退出
 - 修改commit 的具体信息 git commit --amend ，保存并继续下一条 git rebase --continue ，直到全部完成
- 生成patch
 - git format-patch 365a..4e16 --365a和4e16分别对应两次提交的名称
 - git format-patch 4e16 某次提交之后的所有patch
 - git format-patch -2 生成距离HEAD最近的n个patch
- 应用patch
 - 先检查patch文件：git apply --stat newpatch.patch
 - 检查能否应用成功：git apply --check newpatch.patch
 - 打补丁：git am --signoff < newpatch.patch




网卡相关
--------
- ethtool -i ib5e-0 *驱动信息*
- ethtool ib5e-0 *带宽信息*


bash
---------
| history显示时间 |
| -------------- |
| export HISTTIMEFORMAT="%F %T " |
| 编辑/etc/bashrc文件，加入如下几行：HISTTIMEFORMAT="%F %T "， export HISTTIMEFORMAT |


调试python代码
----
- supervisorctl restart all
- /var/log/supervisor/onestor-leader-stderr + tab(语法检查)

查看版本
-----
- cat /etc/os-release
- uname -v/a
- cat /etc/onestor*version

文件复制
----
- scp local_file remote_username@remote_ip:remote_folder
- scp local_file remote_username@remote_ip:remote_file

多路径
----
- multipath -v 3
- cat /proc/partitions
- 查看scsi id
```bash
for i in `cat /proc/partitions | awk '{print $4}' |grep sd`; do echo "### $i: `/lib/udev/scsi_id -g /dev/$i`"; done
```

数据库操作
------
### mariadb
1. mariadbsql -p27HbZwr*g 登录
2. select host,user from mysql.user; 查看用户名,主机
3. show columns from dm_port;   查看表的列信息
4. create function myselect() returns INT       return 6; 添加函数

### postgresql
su postgres
psql calamari
1. 列举数据库：\l
2. 选择数据库：\c 数据库名
3. 查看该某个库中的所有表：\dt
4. 切换数据库：\c interface
5. 查看某个库中的某个表结构：\d 表名
6. 查看某个库中某个表的记录：select * from apps limit 1;
7. 显示字符集：\encoding
8. 退出psgl：\q

其他模块命令
----
- 查看引擎状态（down掉前端网会引起引擎迁移）
 - ceph engine dump
 - ceph osd lspools
 - ceph engine dump|grep epoch (_每次引擎迁移版本号都会变_)
 - ceph engine dump [版本号]（_查看对应版本的引擎状态_)

bash快捷键
---------
- Ctrl + u/k 从光标处删除至命令行首/尾
- alt + f/b 单词前后移动
- Ctrl + y 粘贴至光标后
- history + ！[lineNum] 执行历史命令
- ctrlR 多次按

常用脚本
-----
- CURL请求构造
``` bash
while true;do curl -X POST "http://172.17.136.111/api/v3/onestor/e7e17aa6-2713-4ccb-8ff3-ed4c8a2a0258/request" -H "Content-Type: application/json;charset=UTF-8" -H "Cookie: XSRF-TOKEN=CvGr3DIHbLflGZIK1SSgWNBp93kOMS9r; calamari_sessionid=ylgfltli09h4erugfenppx8mncdxhezi; token=undefined" -H "X-XSRF-TOKEN: CvGr3DIHbLflGZIK1SSgWNBp93kOMS9r" -d '{"comp":"COMP_BLK","op":"LUN_group_exist_check","data":{"diskpool_name":"d1","lun_group_name":"1","nodepool_name":"pool111"},"__async__":true}';done
```

- tgt命令行请求构造

``` bash
#!/bin/bash
COUNT=0
INITIATORS="["
INITIATOR_PRE="{'chap_id_in':'','chap_id_out':'',"

while((${COUNT}<=100))
do
        INITIATOR="'iqn_name':'iqn.1994-05.com.redhat:00112233445${COUNT}'}"
        INITIATOR="${INITIATOR_PRE}${INITIATOR}"
        INITIATORS="${INITIATORS}${INITIATOR},"
        let "COUNT++"
done

INITIATOR="'iqn_name':'iqn.1994-05.com.redhat:00112233445${COUNT}'}"
INITIATOR="${INITIATOR_PRE}${INITIATOR}"
INITIATORS="${INITIATORS}${INITIATOR}]"

onestor tgt TGT_host_create -d "{'host_name_tgt':'host_zp_116','host_os_type':'Linux','host_ip_tgt':'','host_description':'','initiators':${INITIATORS},'nodepool_name':'np0','__async__':'true'}"
```

- iface构造

``` bash
#!/bin/bash

INITIATOR_PRE="iqn.1994-05.com.redhat:00112233445"
IFACE_PRE="myiface"
IPADDR="172.17.136.116"
COUNT=0

while(($COUNT<=50))
do
        INITIATOR="${INITIATOR_PRE}${COUNT}"
        IFACE="${IFACE_PRE}${COUNT}"
        #iscsiadm -m iface -I ${IFACE} -o new
        #iscsiadm -m iface -I ${IFACE} -o update -n iface.initiatorname -v ${INITIATOR}
        #iscsiadm -m discovery -t st -p ${IPADDR} -I ${IFACE} -l
        iscsiadm -m iface -I ${IFACE} -o delete
        let "COUNT++"
done
```

vdbench
----
- vdbench使用 [裸盘和文件系统测试-全套服务](https://blog.51cto.com/liangchaoxi/4713043)
- vdbench使用 [生成文件全解析](https://www.cnblogs.com/luxf0/p/13321077.html)





