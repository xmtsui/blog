---
layout: post
title: [distributed-consensus overview]
categories:
  - ha
---

## 名词解释
1. Two Generals’ Problem
  两军问题
2. Byzantine failures
  拜占庭将军问题
3. liveness
  活跃性。通常指分布式系统的可用性。需要关注的是paxos存在liveness问题，但可以通过一定的变通的做法，来避免。
4. safety
  安全性。通常指分布式系统的一致性。值得一提的是FLP定理否定了同时满足safety 和 liveness 的一致性协议的存在。

## 前提
本文所聊的一致性算法，有如下特点：
* 确保安全性（从来不会返回一个错误的结果），即使在所有的非拜占庭（Non-Byzantine）情况下，包括网络延迟、分区、丢包、冗余和乱序的情况下。（拜占庭问题，需要PBFT-Practical Byzantine Fault Tolerance 这样的算法解决，比较高端，本文不讨论）

* 高可用性，只要集群中的大部分机器都能运行，可以互相通信并且可以和客户端通信，这个集群就可用。因此，一般来说，一个拥有 5 台机器的集群可以容忍其中的 2 台的失败（fail）。服务器停止工作了我们就认为它失败（fail）了，没准一会当它们拥有稳定的存储时就能从中恢复过来，重新加入到集群中。

* 不依赖时序保证一致性，时钟错误和极端情况下的消息延迟在最坏的情况下才会引起可用性问题。

* 通常情况下，一条命令能够尽可能快的在大多数节点对一轮远程调用作出相应时完成，一少部分慢的机器不会影响系统的整体性能。

## 算法介绍
1. 2PC
  常见的事务中间件都会支持2PC。比较经典的例子是oracle的tuxedo以及IBM的cics。
2. 3PC
  相比2PC增加了一个准备阶段
3. 主从复制
  * 异步复制
    可靠性较差
  * 同步复制
    吞吐量较差
  * 混合模式
    兼顾可靠性与吞吐量
  * quorum投票复制
    要么牺牲写性能，要么牺牲读性能的一个折中方案。
    quorum是分布式系统中用来保证数据冗余与最终一致性的投票算法，其数学思想来自鸽笼原理。常见的典型实现是apache bookkeeper.
3. paxos
  * basic paxos
    经典的二阶段的paxos实现，《The Part-Time Parliament》有点难懂，可以看《paxos made simple》来理解。
  * multi-paxos
    将原来两阶段过程简化为了一阶段，从而加快提交速度。但Multi-Paxos要求在各个Proposer中有唯一的Leader。而且leader在提交proposal之前，必须要经历二阶段的准备，达到要求的一致状态。
4. raft
  * 易于理解的，同时易于工程化实践的分布式一致性算法。其主要包括领导选取（leader election）、日志复制（log replication）、安全（safety）和成员变化（membership changes）四个部分。建议看[raft经典动画](http://thesecretlivesofdata.com/raft/)来了解。 需要注意的是Raft协议强调日志的连续性，multi-paxos则允许日志有空洞。
5. ISR机制
  * 特指kafka实现的主备方案。Kafka并没有使用多数投票机制来保证主备数据的一致性，而是提出了ISR的机制（In-Sync Replicas）的机制来保证数据一致性。需要注意的是，如果设定ISR集合大小为f+1，那么可以最多允许f个副本故障，而对于多数投票机制来说，则需要2f+1个副本才能达到相同的容错性。

## 常见的复制策略
1. primary-backup replication  
  * synchronize  
    客户端需要同步等所有副本返回结果，客户端的请求速度取决于最慢的那一个副本。读写可以任意。
  * asynchronize  
    主收到请求，即确认本次客户端的请求。但此时如果主挂掉，则可能会有数据丢失。同时，为了防止脏读，必须要在主读数据。如果在备读，则需要容忍数据不一致性。
  * 混合模式
  * quorum-based   
    主收到请求，在部分副本确认后，即确认客户端的请求。此时主挂掉，则不会丢失数据。如果要强一致读，则需要满足W+R>N。当看重写效率，读效率要求不高时，可以W小一点，R大一点；当看重读效率，写效率要求不高时，可以W大一点，R小一点。

## 未完待续

## refer
参考[readqueue-01](http://xmtsui.github.io/blog/readqueue/2017/03/14/readqueue-01.html)中所列的文章。
