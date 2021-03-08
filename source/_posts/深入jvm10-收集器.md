---
title: 深入jvm10-收集器.md
date: 2019-07-15 22:28:29
tags:
---

新生代的收集器

## Seria
 单线程收集器，工作是必须 stop the word
 可控制参数： -XX:SurvivorRatio , -XX:PretenureSizeThreadHold -XX:HandlePromotionFailure 
 算法：采用复制算法
## ParNew

 多线程收集器，工作是必须 stop the word
 可控制参数： -XX:SurvivorRatio , -XX:PretenureSizeThreadHold -XX:HandlePromotionFailure -XX:+UseParNewGC 
 算法：采用复制算法
 默认与CMS结合作为新生代收集器

## Parallel Scavenge

重点关注吞吐量 throughput = 运行代码时间/(运行代码时间+垃圾收集时间)
参数控制：
-XX:+MaxGCPauseMillis 最大垃圾收集停顿时间

-XX:GCTimeRatio:直接设置吞吐量 （垃圾收集时间占总时间的比率）
因为其没有使用传统的GC收集器，暂时无法与CMS一起配合使用


老年代的收集器
## Seria Old
1：与Parallel Scavenge配合使用
2：CMS的后备方案（Concurrent Model failure的时候）

## Parllel Old
 Parallel Scavenge 的老年代版本
 算法：标记-整理

## CMS

算法：标记-清除
步骤：
1 ，初始标记 （标记GC-root能关联到的对象）
2 ，并发标记
3 ，重新标记
4 ，并发清除

## G1
##  
