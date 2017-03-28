---
layout: post
title: [distributed-consensus overview]
categories:
  - ha
---

## 名词解释
1. Quorom: 分布式系统中用来保证数据冗余与最终一致性的投票算法，其数学思想来自鸽笼原理。
2. Two Generals’ Problem
3. Byzantine failures

## 算法介绍
1. 2PC


## 常见的复制策略
1. primary-backup replication  
  * synchronize  
    同步等所有副本返回结果，客户端的请求速度取决于最慢的那一个副本。读写可以任意。
  * asynchronize  
    主收到请求，即确认本次客户端的请求。但此时如果主挂掉，则可能会有数据丢失。同时，为了防止脏读，必须要在主读数据。如果在备读，则需要容忍数据不一致性。
  * quorum-based
    主收到请求，在部分副本确认后，即确认客户端的请求。此时主挂掉，则不会丢失数据。如果要强一致读，则需要满足W+R>N。当看重写效率，读效率要求不高时，可以W小一点，R大一点；当看重读效率，写效率要求不高时，可以W大一点，R小一点。


## refer
1. [ 大数据技术系列----副本更新策略](http://blog.csdn.net/ni_guang2010/article/details/48493507)