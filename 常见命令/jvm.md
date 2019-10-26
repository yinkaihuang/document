# JVM相关命令



**查看java进程启动参数配置**

```
jinfo -flags pid
```



**查看当前java进程中线程栈**

```
jstack pid
```



**查看当前堆信息**

```
jmap -heap pid
```



**查看java进程中对象数量**

```
jmap -histo:live pid
```



**导出当前堆快照**

```
jmap -dump:live,format=b,file=文件位置 pid
jmap -dump:format=b,file=文件位置 pid
```



**动态查看java垃圾回收情况**

```
jstat -gcutil pid 2s
```

