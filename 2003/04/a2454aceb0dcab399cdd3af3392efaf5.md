> 分享根据[《Linux性能优化》](https://time.geekbang.org/column/intro/140)课程图片整理的笔记。

前言
--

实验环境：ubuntu 18.04、切换至root权限下操作  

查看命令详细使用方法，可以：**man xxx**  
提及到路径的话，一般可以 **cat /x/xx/xxx**  
提及到工具的话，如果没安装，可以：**apt-get install xxx**

CPU篇
----

### CPU性能指标

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/qqYo0VWsrr.png!large)

### 根据指标找工具

性能指标

工具

说明

平均负载

uptime  
top

uptime最简单；  
top提供了更全的指标

系统整体CPU使用率

vmstat  
mpstat  
top  
sar  
/proc/stat

top、vmstat、mpstat只可以动态查看，  
  
/proc/stat/是其他性能工具的数据来源

进程CPU使用率

top  
pidstat  
ps  
htop  
atop

top和ps可以按CPU使用率给进程排序，  
而pidstat只显示实际用了CPU的进程  
  
htop和atop以不同颜色显示更为直观

系统上下文切换

vmstat

除了上下文切换次数，  
还提供运行状态和不可中断状态进程的数量

进程上下文切换

pidstat

注意加上-w 选项

软中断

top  
/proc/softirqs  
mpstat

top提供软中断CPU使用率，  
而/proc/softirqs和mpstat提供了各种软中断在每个CPU上的运行次数

硬中断

vmstat  
/proc/interrupts

vmstat提供总的中断次数，  
而/proc/interrupts提供各种中断在每个CPU上运行的累积次数

网络

dstat  
sar  
tcpdump

dstat和sar提供总的网络接收和发送情况，  
而tcpdump则是动态抓取正在进行的网络通讯

I/O

dstat  
sar

dstat和sar都提供了I/O的整体情况

CPU个数

/proc/cpuinfo  
lscpu

lscpu更直观

事件剖析

perf  
execsnoop

perf可以用来分析CPU的缓存以及内核调用链，execsnoop用来监控短时进程

### 根据工具查指标

性能工具

CUP性能指标

uptime

平均负载

top

平均负载、运行队列、整体的CPU使用率以及每个进程的状态和  
CPU使用率

htop

top增强版，以不同颜色区分不同类型的进程，更直观

atop

CPU、内存、磁盘和网络等各种资源的全面监控

vmstat

系统整体的CPU使用率、上下文切换次数、中断次数、  
还包括处于运行和不可中断状态的进程数量

mpstat

每个CPU的使用率和软中断次数

pidstat

进程和线程的CPU使用率、中断上下文切换次数

/proc/softirqs

软中断类型和在每个CPU上的累积中断次数

/proc/interrupts

硬中断类型和在每个CPU上的累积中断次数

ps

每个进程的状态和CPU使用率

pstree

进程的父子关系

dstat

系统整体的CPU使用率

sar

系统的整体CPU使用率，包括可配置的历史数据

strace

进程的系统调用

perf

CPU性能事件剖析，如调用链分析、CPU缓存、CPU调度

execsnoop

监控短时进程

### CPU性能优化分析

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/Oz8dERwnge.png!large)

内存篇
---

### 内存性能指标

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/0RmdwBAd5N.png!large)

### 根据指标找工具

内存指标

性能工具

系统已用、可用、剩余内存

free  
vmstat  
sar  
/proc/meminfo

进程虚拟内存、常驻内存、共享内存

ps  
top

进程内存分布

pmap

进程Swap换出内存

top  
/proc/pid/status

进程缺页异常

ps  
top

系统换页情况

sar

缓存（Cache）/缓冲区（Buffer）用量

free  
vmstat  
sar  
cachestat

缓存（Cache）/缓冲区（Buffer）用量

cachetop

SWAP已用空间和剩余空间

free  
sar

Swap换入换出

vmstat

内存泄漏检测

memleak  
valgrind

指定文件的缓存大小

pcstat

### 根据工具查指标

性能工具

内存指标

free  
/proc/meninfo

系统已用、可用、剩余内存以及缓存和缓冲区的使用量

top  
ps

进程虚拟、常驻、共享内存以及缺页异常

vmstat

系统剩余内存、缓存、缓冲区、换入、换出

sar

系统部分内存换页情况、内存使用率、缓存和缓冲区用量  
以及Swap

cachestat

系统缓存和缓冲区的命中率

cachetop

进程缓存和缓冲区的命中率

slabtop

系统Slab缓存使用情况

/proc/pid/status

进程Swap内存等

/proc/pid/smaps  
pmap

进程地址空间和内存状态

valgrind

进程内存错误检查器，用来检测内存初始化、泄漏、  
越界访问等各种内存错误

memleak

内存泄漏检测

pcstat

查看指定文件的缓存情况

### 内存性能优化分析

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/4snGgzRjRQ.png!large)

I/O篇
----

### I/O性能指标

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/LUAA6oVA94.png!large)

### 根据指标找工具

性能指标

工具

说明

文件系统空间容量、使用量以及剩余空间

df

详细文档见  
info coreutils 'df invocation'

索引结点容量、使用量以及剩余量

df

使用 -i 选项

页面缓存和可回收Slab缓存

/proc/meminfo  
sar、vmstat

使用 sar -r 选项

缓冲区

/proc/meminfo  
sar、vmstat

使用 sar -r 选项

目录项、索引节点以及文件系统的缓存

/proc/slabinfo  
slabtop

slabtop更直观

磁盘I/O使用率、IOPS、  
吞吐量、响应时间、I/O平均大小  
以及等待队列长度

iostat  
sar、dstat

使用 iostat -d -x 或 sar -d 选项

进程I/O大小以及I/O延迟

pidstat  
iotop

使用 pidstat -d 选项

块设备I/O事件跟踪

blktrace

示例：blktrace -d /dev/sda -o- （管道符） blkparse -i-

进程I/O系统调用跟踪

strace

通过系统调用跟踪进程的I/O

进程块设备I/O大小跟踪

biosnoop  
biotop

需要安装bcc软件包

### 根据工具查指标

性能工具

性能指标

iostat

磁盘I/O使用率、IOPS、吞吐量、响应时间、I/O平均大小以及等待队列长度

pidstat

进程I/O大小以及I/O延迟

sar

磁盘I/O使用率、IOPS、吞吐量以及响应时间

dstat

磁盘I/O、IPOS以及吞吐量

iotop

按I/O大小对进程排序

slabtop

目录项、索引结点以及文件系统的缓存

/proc/slabinfo

目录项、索引结点以及文件系统的缓存

/proc/meminfo

页缓存和可回收Slab缓存

/proc/diskstats

磁盘的IOPS、吞吐量以及延迟

/proc/pid/io

进程IOPS、I/O大小以及I/O延迟

vmstat

缓存和缓冲区用量汇总

blktrace

跟踪块设备I/O事件

biosnoop

跟踪进程的块设备I/O大小

biotop

跟踪进程的并按I/O大小排序

strace

跟踪进程的I/O系统调用

perf

跟踪内核中的I/O事件

df

磁盘空间和索引结点使用量和剩余量

mount

文件系统的挂载路径以及挂载参数

du

目录占用的磁盘空间大小

tune2fs

显示和设置文件系统参数

hdparam

显示和设置磁盘参数

### I/O性能优化分析

![Linux 性能优化](https://cdn.learnku.com/uploads/images/202001/05/1875/4Na11isdnY.png!large)

网络篇
---

### 根据指标找工具

性能指标

工具

说明

吞吐量（BPS）

sar  
nethogs  
iftop

分别可以查看网络接口、进程以及IP地址的网络吞吐量

PPS

sar  
/proc/net/dev

查看网络接口的PPS

连接数

nestat  
ss

查看网络连接数

延迟

ping  
hping3

通过ICMP、TCP等测试网络延迟

连接跟踪数

conntrack

查看和管理连接跟踪状况

路由

mtr  
route  
traceroute

查看路由并测试链路信息

DNS

dig  
nslookup

排查DNS解析问题

防火墙和NAT

iptables

配置和管理防火墙及NAT规则

网卡功能

ethtool

查看和配置网络接口的功能

抓包

tcpdump  
Wireshark

抓包分析网络流量

内核协议栈跟踪

bcc  
systemtap

动态跟踪内核协议栈的行为

### 根据工具查指标

性能工具

主要功能

ifconfig  
ip

配置和查看网络接口

ss

查看网络连接数

sar  
/proc/net/dev/sys/class/net/eth0/statistics

查看网络接口的收发情况

nethogs

查看进程的网络收发情况

iftop

查看IP的网络收发情况

ethool

查看和配置网络接口

conntrack

查看和管理连接跟踪状况

nslookup  
dig

排查DNS解析问题

mtr  
route  
traceroute

查看路由并测试链路信息

ping  
hping3

测试网络延迟

tcpdump

网络抓包工具

Wireshark

网络抓包和图形界面分析工具

iptables

配置和管理防火墙及NAT规则

perf

剖析内核协议栈的性能

systemtap  
bcc

动态追踪内核协议栈的行为

结语
--

“纸上得来终觉浅，绝知此事要躬行” 。在此，提供本人实践的课程案例，希望对你有帮助。

[案例：某个应用的 CPU 使用率居然达到 100%，我该怎么办？](https://learnku.com/articles/39474)  
[案例：系统的 CPU 使用率很高，但为啥却找不到高 CPU 的应用？](https://learnku.com/articles/39516)  
[案例：系统中出现大量不可中断进程（D）和僵尸进程（Z）怎么办？](https://learnku.com/articles/39851)

[性能优化](https://learnku.com/blog/GetaChan/tags/performance-optimization_49639)

> 本作品采用[《CC 协议》](https://learnku.com/docs/guide/cc4.0/6589)，转载必须注明作者和本文链接