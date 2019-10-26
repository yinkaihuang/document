# 常见GC配置

**cms**

```
//设置最小堆空间和最大堆空间大小
-Xms14g -Xmx14g 
//设置每个线程最多可用空间
-Xss512k 
//设置方法区固定大小为384m
-XX:MetaspaceSize=384m -XX:MaxMetaspaceSize=384m 
//这里设置年轻代的最大空间和最小空间如果Xms和Xmx相同可以用（-Xmn11g)替换下面
-XX:NewSize=11g -XX:MaxNewSize=11g （推荐用-Xmn11g）
//设置survivor和eden比例 (survivor大小为年轻代大小*（2+survivorRatio）)
-XX:SurvivorRatio=18 
//设置最大直接内存的大小
-XX:MaxDirectMemorySize=2g 
//年轻代使用ParNewGC
-XX:+UseParNewGC 
//并发收集垃圾时启动线程数量为4个
-XX:ParallelGCThreads=4 
//年轻代需要经过15次提升才能进入到老年代
-XX:MaxTenuringThreshold=15 
//使用老年代使用CMS+Serial Older 组合垃圾回收器
-XX:+UseConcMarkSweepGC 
//取消System.gc()
-XX:+DisableExplicitGC  (这里和下面参数无法共同存在)
//是指设定CMS在对内存占用率达到70%的时候开始GC(因为CMS会有浮动垃圾,所以一般都较早启动GC)并且一直使用这个比例;
-XX:CMSInitiatingOccupancyFraction=70 
-XX:+UseCMSInitiatingOccupancyOnly 
//cms执行remark进行一次minor gc并开启并行进行remark
-XX:+CMSScavengeBeforeRemark 
-XX:+CMSParallelRemarkEnabled 
//在FullGC时是否进行内存压缩默认开启，并且设置多少次cms gc后进行一次内存压缩
-XX:+UseCMSCompactAtFullCollection 
-XX:CMSFullGCsBeforeCompaction=9
//配置在full gc 开始之前进行minor gc（也就是执行yong gc）
-XX:+ScavengeBeforeFullGC 
//配置让cms能够回收metadata空间
-XX:+CMSClassUnloadingEnabled
-XX:+CMSPermGenSweepingEnabled 

-XX:SoftRefLRUPolicyMSPerMB=0 
-XX:-ReduceInitialCardMarks 
//允许cms回收堆外内存空间（也就是dirct memory）
-XX:+ExplicitGCInvokesConcurrent （将System.gc()采用cms进行回收而不是full gc）
//配置输出日志详细信息并指定文件件
-XX:+PrintGCDetails 
-XX:+PrintGCDateStamps 
-XX:+PrintGCApplicationConcurrentTime 
-XX:+PrintHeapAtGC 
-Xloggc:/data/applogs/heap_trace.log 
//配置堆内存溢出输出当前快照位置
-XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=/data/dump/error.hprof

可选配置参数（一般用来定位jvm性能问题)

//对于是否线程到达安全点时间引起的原因， 我们加上显示 Stop 时间与 Safepoint 的相关参数
-XX:+PrintGCApplicationStoppedTime 
-XX:+PrintSafepointStatistics 
-XX:PrintSafepointStatisticsCount=1
//打印当前堆中对像年龄分布情况
-XX:+PrintTenuringDistribution

//跟踪系统内的软引用，弱引用，虚引用和finallize队列。
-XX:+PrintReferenceGC
```



**G1**

```
//使用 G1 (Garbage First) 垃圾收集器
-XX:+UseG1GC 
//设置最大GC停顿时间(GC pause time)指标(target). 这是一个软性指标(soft goal), JVM 会尽量去达成这个目标.
-XX:MaxGCPauseMillis=200 
//启动并发GC周期时的堆内存占用百分比. G1之类的垃圾收集器用它来触发并发GC周期,基于整个堆的使用率,而不只是某一代内存的使用比. 值为 0 则表示"一直执行GC循环". 默认值为 45.
-XX:InitiatingHeapOccupancyPercent=45 
//新生代与老生代(new/old generation)的大小比例(Ratio). 默认值为 2.
-XX:NewRatio=2 
eden/survivor 空间大小的比例(Ratio). 默认值为 8.
-XX:SurvivorRatio=8 
//提升年老代的最大临界值(tenuring threshold). 默认值为 15.
-XX:MaxTenuringThreshold=15
//设置垃圾收集器在并行阶段使用的线程数,默认值随JVM运行的平台不同而不同.
-XX:ParallelGCThreads=5
//并发垃圾收集器使用的线程数量. 默认值随JVM运行的平台不同而不同.
-XX:ConcGCThreads=5
//设置堆内存保留为假天花板的总量,以降低提升失败的可能性. 默认值是 10.
-XX:G1ReservePercent=10 
//使用G1时Java堆会被分为大小统一的的区(region)。此参数可以指定每个heap区的大小. 默认值将根据 heap size 算出最优解. 最小值为 1Mb, 最大值为 32Mb.
-XX:G1HeapRegionSize=2 
-XX:+PrintGCDetails 
-XX:+ExplicitGCInvokesConcurrent
```

