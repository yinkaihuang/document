# Linux常见命令

> 文件相关
> 日志相关
> 网络相关
> 磁盘相关
> 内存相关
> 其他

**文件相关**
```python
递归创建文件目录：
mkdir -p /root/test/hello

删除文件：
rm -rf /root/test

查看当前文件权限值：
stat 文件名

复制文件：
cp /root/test /root/hello

压缩文件为.tar.gz：
tar zcvf test.tar.gz 被压缩文件或者路径

解压文件：
tar -zxvf /root/test/test.tar.gz

移动文件：
mv 原文件  移动后文件

修改文件执行权限：
chmod 777 -R 文件名称

远程复制：
scp test.tar.gz 192.168.40.33:/temp/test/ （将当前test.tar.gz文件复制到192.168.40.33的/temp/test目录下面）

查找某个文件位置：
find / -name 文件名称 （模糊匹配 find / -name '*test'）

显示有些无法显示全的数据（利用管道）
ps auxw |cat
```

**日志相关**

```python
动态查看日志最后100行:
tail -100f test.log

查看日志开通100行：
head -n 100 test.log 

统计日志中出现某个字符行数：
cat test.log|grep 'error' | wc -l

输出日志中某个匹配的字符前1行后10行：
cat test.log|grep 'error' -B 1 -A 10

```

**网络相关**

```python
统计当前各种连接数量：
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

查看当前tcp连接情况：
netstat -ntp

查看当前tcp相关配置：
sysctl -a|grep net.ipv4.tcp*

window下查看tcp情况：
netstat -ano | findstr 9090

```

**内存相关**

```python
查看内存使用情况:
free -h

查看详细使用:
cat /proc/meminfo

手动释放内存:
echo 1 > /proc/sys/vm/drop_caches 

查看所有进程内存使用情况:
ps -e -o 'pid,comm,args,pcpu,rsz,vsz,stime,user,uid'  其中rsz是是实际内存

使用top命令查看:
m 切换显示内存信息,M 根据驻留内存大小排序,c 切换显示命令名称和完整命令行

动态查看内存变化:
watch -n 2 -d free -m(这里单位是M)

查看负载:
uptime

查看服务器上oom情况:
dmesg|grep oom

查看tmpfs配置情况<注意tmpfs使用mmap技术将硬盘上的数据映射到内存中>:
1查看那些文件采用tmpfs
cat /proc/mounts |grep tmpfs
2查看文件使用情况
df -h 

```

**cpu相关**

```python
查看进程中线程消耗情况:
top -Hp 9179
htop -p pid

通过strace看下线程调用情况:
strace -p tid(线程id)

将线程id转变为16进制:
printf '%x\n' tid

根据tid找到java线程执行情况：
jstact pid |grep '0x线程id(16进制)' -A 15 -B 1 

查看cpu的核数：
lscpu
```

**进程相关**

```python
定位僵尸进程以及该僵尸进程的父进程:
ps -A -ostat,ppid,pid,cmd |grep -e '^[Zz]'

使用Kill -HUP 僵尸进程的父进程:
kill -HUP 僵尸进程父ID

查看某个进程上面的线程数量：
pstree -p id |wc -l

查看进程上下文切换数量：
pidstat -w pid

查看线程上下文切换次数：
pidstat -wt tid

```

**其他命令**

```
如何查看容器中的某个进程:
ps auxw|grep mysql 

查看目录挂载情况(list block):
lsblk 

查看文件目录被那个进程占用:
lsof +d /root/test/

查看当前pid进程启动时的工作目录:
pwdx pid

查看登录执行命令记录:
history
>>如果记录没有显示时间 
1.export HISTTIMEFORMAT='%F %T '(临时生效) 2.echo 'HISTTIMEFORMAT="%F %T "' 
>> ~/.bashrc  再执行 source~/.bashrc

显示登录失败的记录:
lastb
```