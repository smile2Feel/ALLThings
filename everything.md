1. [/usr/include 和 /usr/local/include](https://www.linuxquestions.org/questions/linux-software-2/please-difference-between-usr-include-and-usr-local-include-818767/)

2.  [Linux Filesystem Hierarchy](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/index.html)

3. [linux信号量机制分析](https://www.cnblogs.com/LoyenWang/p/12907230.html)

   - [为什么会产生虚假唤醒？spurious wakeup](https://www.zhihu.com/question/271521213)

4. [浅析 Linux 初始化 init 系统:sysvinit, systemd, Upstart](https://zhuanlan.zhihu.com/p/49556226)

5. [grep in a tar.gz Archive](https://www.baeldung.com/linux/grep-tar-archive)

6. [ssh/sshd的调试模式](https://developer.aliyun.com/article/628175)

7. [如何ping指定ip的端口号](https://www.jianshu.com/p/fbdf744a3fbd)

8. [tcpdump抓包简单指令](https://www.cnblogs.com/chyingp/p/linux-command-tcpdump.html)

9. [cgroup工具安装](https://developer.aliyun.com/article/43544)

10. [yum源安装](https://blog.csdn.net/inslow/article/details/54177191)

12. [VS code for Windows](https://code.visualstudio.com/docs/setup/windows)

    - [includePath添加](https://www.zhihu.com/question/44844423)
    - [全局include path 和工作区 include path](https://www.cnblogs.com/unrulife/p/14319466.html)

    

    

    ## linux cmd

    1. [traceroute](https://wangchujiang.com/linux-command/c/traceroute.html)

    2. [ps](http://c.biancheng.net/view/1062.html)

    3. [nc](https://blog.csdn.net/weixin_42542420/article/details/112486792)

    4. [dd](https://www.geeksforgeeks.org/dd-command-linux/)

       

## linux API

1. [sockaddr_in, sockaddr](https://stackoverflow.com/questions/21099041/why-do-we-cast-sockaddr-in-to-sockaddr-when-calling-bind)

2. [sockaddr_storage](https://stackoverflow.com/questions/21883030/how-to-fill-sockaddr-storage)

3. [AF-independent](https://www.kame.net/newsletter/19980604/)

4. [sigaction](https://stackoverflow.com/questions/9302464/how-do-i-remove-a-signal-handler)

5. [SIG_DFL](https://stackoverflow.com/questions/33922223/what-exactly-sig-dfl-do)

   





## shell script

2. [shell中字符串比较和模糊比较说明](https://cloud.tencent.com/developer/article/1579626)
3. [Linux Shell之递归读取指定目录下的所有文件](https://blog.csdn.net/cnds123321/article/details/121919254)



## ceph cmd

1. [ceph config](https://docs.ceph.com/en/quincy/man/8/ceph/)
2. 



## git learn

1. git reflog, git reset --hard HEAD^/commit-id 前几位即可
2.  git diff HEAD -- readme.txt 
3.   *In general*, `soft: stage everything`, `mixed: unstage everything`, `hard: ignore everything` up to the commit I'm resetting from 
4. git checkout -- {fileName} 清除工作区的修改，暂存区需要clean
5.  `git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。 
6. 删除文件：git rm，git commit;删错了，git checkout -- {fileName}



## Shell script exploit

- PATH
  - export PATH=~/bin:"$PATH", (.bashrc)
  - source .bashrc

###  data miscompare

- 找对象

  - rbd info .test2.rbd/j100

    rbd image 'j100':
            size 10240 MB in 2560 objects
            order 22 (4096 KB objects)
            data_pool: p1
            block_name_prefix: rbd_data.65539.57fd84f02b89
            format: 2
            features: layering, deep-flatten, data-pool, thick-optimization-logic
            flags:
            create_timestamp: Wed Mar  1 14:04:39 2023

  - 算对象偏移 （offset << 22 对象，对象内偏移（16进制））

  - rados get -p p1 rbd_data.65539.57fd84f02b89.00000000000004c4 obj.out

  - rados stat -p p1 rbd_data.65539.57fd8e260331.00000000000004b4 --debug_objecter=20

  - zgrep "rbd_data.65539.57fd8e260331.00000000000004b4.*3145728" ceph-dse.engine.65542.8.log-2023-03-14-222801.gz > dse_opera (获取dse日志，根据tid进行io跟踪)

  
  
  
  
  
  
  ## daily sg_cmd
  
  - sg_read
  
    
  
  
  
  ### perf
  
  1、 生成perf数据 perf record -e cpu-clock -g --call-graph dwarf -p 34696 
  
  ​		ctrl+c 
  
  ​		perf report -i perf.data
  
  2、第二步
  
  用perf script工具对perf.data进行解析
  
  perf script -i perf.data &> perf.unfold
  
  3、第三步
  
  将perf.unfold中的符号进行折叠：
  
  \#./stackcollapse-perf.pl ../perf.unfold &> ../perf.folded
  
  4、最后生成svg图：
  
  ./flamegraph.pl ../perf.folded > ../perf.svg
  
  
  
  
  
  ### docker
  
  - docker container ls --all
  - docker image ls
  - docker ps -a
  - docker container start 3b054459c5c1
  - docker exec -it 3b054459c5c1 /bin/bash
  -  docker run -it -d --privileged --cap-add=ALL --name ceph_v15 -p 22223:22 -p 6666:6666 -p 42922:42922 -p 44922:44922 -p 46922:46922 -v /home/xb/project/ceph/xb/ceph:/home/xb/project/ceph/xb/ceph ceph_centos7:v15.2.17
  - docker exec -u root -it ceph_v15 bash -c 'cd /home/xb/project/ceph/xb/ceph;exec "${SHELL:-sh}"' 



### ceph 编译运行

- FS=0 OSD=3 MON=3 MGR=3 ../src/vstart.sh --debug --new （build目录下）
- ./do_cmake.sh -DWITH_TESTS=ON