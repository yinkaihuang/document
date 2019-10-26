# **Innodb page clean 线程 (一) 基础**

**一、page clean线程概念**

Innodb中page clean线程将脏数据写入到磁盘，脏数据写盘后相应的redo就可以覆盖，然后达到redo循环使用的目的。在5.7中参数可以开启多个page clean线程服务于多个innodb buffer实例如下：

The innodb_page_cleaners default value was changed from 1 to 4 in MySQL 5.7. If the number of page cleaner threads exceeds the number of buffer pool instances, innodb_page_cleaners is automatically set to the same value as innodb_buffer_pool_instances. 

实际上在内部实现中如果page clean线程为4个那么包含一个协调工作线程和三个工作线程，这个协调工作线程也要完成一部分工作。在MySQL中我们可以通过语句查看到这些工作线程：

|     17 |        57982 | innodb/page_cleaner_thread      |    NULL | BACKGROUND | NULL   | NULL         | |     18 |        57983 | innodb/page_cleaner_thread      |    NULL | BACKGROUND | NULL   | NULL         | |     19 |        57984 | innodb/page_cleaner_thread      |    NULL | BACKGROUND | NULL   | NULL         | |     20 |        57985 | innodb/page_cleaner_thread      |    NULL | BACKGROUND | NULL   | NULL         | 

实际上在我浅析分析中发现，所有的工作线程都是不断轮序每一个和buffer instance对应的槽(slot)，直到所有的buffer instance都已经进行了刷脏工作为止，并没有固定那个工作线程服务于那个buffer instance实例。

**二、刷新方式**

总的来说page clean线程刷新的方式分为三种如下：

**1、 活跃刷新**

一般来讲我们线上的数据库一般都处于活跃状态，只要有DML/DDL等用到语句都会处于活跃状态，但是SELECT不包含在活跃状态下。这种状态下刷新会开启一个协调工作线程和多个工作线程同时工作，这种状态其刷新的块数算法为（page_cleaner_flush_pages_recommendation函数）：

- (根据参数计算出来的页数量 +以往每秒刷新页的数量+根据target lsn计算出来的一个需要刷新的块数)/3

实际上这里需要关注的就是**根据参数计算出来的页数量**，算法大概如下（af_get_pct_for_dirty函数）：

如果innodb_max_dirty_pages_pct_lwm没有开：       如果脏数据比率大于等于innodb_max_dirty_pages_pct的设置：                则返回100% 如果innodb_max_dirty_pages_pct_lwm开启：        如果脏数据比率大于等于innodb_max_dirty_pages_pct_lwm：                  则返回(脏数据比率*100)/(innodb_max_dirty_pages_pct+1)这样一个百分比 

我们计上面的百分比为A，除了百分比A还和innodb_adaptive_flushing、innodb_adaptive_flushing_lwm计算出来的百分比有关，我们记做B（af_get_pct_for_lsn函数计算），但是由于参数innodb_cleaner_lsn_age_factor默认设置为high_checkpoint，所以这个百分比比较小，具体算法见后文，其最后取值算法为：

根据参数计算出来的页数量 = MAX(A,B)*innodb_io_capacity 

**2、空闲刷新**

一般情况下除了活跃刷新就是空闲刷新，空闲的情况下因为服务器IO应该比较空闲，所以Innodb使用协调工作线程本身进行刷新，刷新的块数计算比较简单就是innodb_io_capacity设置的值。

**3、 同步刷新**

同步刷新则是堵塞刷新，所有需要写脏数据库的用户线程都会堵塞，这是很严重的情况。在checkpoint的时候

会检查或者DML语句执行过程中都会检查redo是否处于一个安全的位置，这是调用log_free_check函数进行，如果认为脏的块数太多，redo已经处于不安全的位置（log_checkpoint_margin），那么同步刷新会被唤醒。

关于这部分在源码部分还会提到。

**三、关于一个警告**

警告如下：

page_cleaner: 1000ms  intended loop took **ms. The settings might not be optimal.((flushed="**" , during the time.) 

实际上这个警告来自于两次刷新时间的检测：

- 本次刷新时间 - 上次刷新时间 > 1秒(睡眠时间)+3秒 则报警告

这个警告一般是IO能力不足，或者参数不够优化的结果，有了上面的基础我们知道这里应该做如下操作：

- innodb_io_capacity 应该降低
- innodb_max_dirty_pages_pct 应该降低
- innodb_max_dirty_pages_pct_lwm 如果设置了应该考虑降低

- - innodb_io_capacity_max 考虑降低涉及到上面说的百分比B的计算（af_get_pct_for_lsn函数）

降低的目的在于减少每次刷新的量，让每次刷新块数更加平均。从而避免page clean 线程爆发性的刷新脏数据库，从而堵塞IO通道。如果慢慢调整后还是不行则考虑IO确实扛不住了。

关于这部分在源码部分还会提到。

后文将会对得到这些结论的源码进行解析。