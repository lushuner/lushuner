---
layout:     post
title:      "Flume MemoryChannel源码分析"
subtitle:   "MemoryChannel源码分析"
date:       2016-06-06 12:00:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Flume
    - 大数据
---


## 前言
之前项目中用到过Flume，当时为了优化Flume性能，对Flume做各种性能测试，在到达瓶颈的时候，开始看一部分Flume源码。当时主要针对Flume的Channel进行性能测试，选择最常用的MemoryChannel，读了一下源码，趁还有印象总结一下。

## MemoryChannel结构图
首先，分析源码后，画出MemoryChannel在Flume Agent中的传输数据的结构图：  
![img](/img/in-post/post-2016-06-06/1.png)  
1. MemoryChannel中的queue是真正存放内存通道event的队列。  
2. batchSize是针对Source和Sink提出的一个概念，它用来限制source和sink对event批量处理的。
即一次性你可以处理batchSize个event，这个一次性就是指在一个事务中。
当你处理的event数量超出了batchSize，那么事务就会提交了。
注意，这里有一个隐晦的地方，就是batchSize一定不能大于transactionCapacity  
3. transactionCapacity则是putList和takeList的容量。
MemoryChannel中维护了三个LinkedBlockingDeque队列：
putList, takeList, queue。  


## MemoryTransaction
MemoryChannel中最重要的内部类MemoryTransaction ,它以事务传输来保证Flume传输数据的可靠性。简单画一下MemoryTransaction与MemoryChannel的类结构关系图：  
![img](/img/in-post/post-2016-06-06/2.png)    
充分体现了设计者面向接口编程的原则啊。  


接下来分析MemoryTransaction的代码：
### MemoryTransaction定义
![img](/img/in-post/post-2016-06-06/3.png)    

### doPut()
![img](/img/in-post/post-2016-06-06/4.png)     
doPut方法核心内容是putList.offer(event)，这个offer方法是将event插入到putList队尾，在插入前会进行预判，如果有足够空间则直接插入，并返回true，如果没有足够空间则不插入，返回false。    
MemoryChannel中的事务是：event从source端经过channel传输到sink端，整个过程是一个事务。   

### doCommit()
![img](/img/in-post/post-2016-06-06/5.png)     
doCommit（）方法核心语句是queue.offer(putList.removeFirst());
将putList中的event提交到queue中。

### doTake()
![img](/img/in-post/post-2016-06-06/6.png)     
将event从queue提交到takeList。

### doRollback()
![img](/img/in-post/post-2016-06-06/7.png)     
事务回滚，如果takeList中的event没有全部成功提交到sink中，则需要事务回滚，即将takeList中事务从尾部开始依次插入到queue的队列首部。
