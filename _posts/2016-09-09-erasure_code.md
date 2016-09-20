---
layout: post
title: erasure code
date: 2016-9-9
categories: multi-core
tags: [总结,知识管理]
description: 下周二paper reading，刚好最近要做erasure code相关的工作，于是选择了2012年ATC的best paper。
---

# What is erasure code?

erasure code将n份原始数据，增加m份parity，并能通过n+m份数据中的任意n份数据恢复原始数据。erasure code的使用分为两个阶段，encode和decode。

## encode

现在我们有的数据是D1到D5，将它排列成5*1作为矩阵D，矩阵B则由单位矩阵和下一部分组成，单位矩阵和D相乘可以得到D，下面一部分和D相乘就得到冗余数据。

![](https://i.imgsafe.org/7d87e01ca1.png)

## decode

当数据出现丢失的时候，我们就需要恢复原始数据，假设现在D1，D4，C2丢失，那么在B和结果矩阵中就需要去掉相应的行，去掉之后，乘积仍然成立，这时候就只用将Survivors和B‘的逆矩阵相乘就可以得到原始数据了。

![](https://i.imgsafe.org/7d88115b5d.png)

## erasure code vs. replication

在前面，如果B矩阵下面的部分使用的是范德蒙矩阵，那么这种code的方式称为Reed-Solomon code，我们比较RS（6，3）和3-replication的性能。

3-replication一般是industry的标准，如果MTTF能达到3-replication的标准，那么就认为可以接受。

分析可以看出RS（6，3）的可靠度达到了3-replication，同时storage的overhead远小于3-replication。但缺点是当需要重组数据的时候，RS6需要读6个fragment才能重组出原始数据，而3-replication可以直接去读原始数据。

那么如果想使用erasure code的时候一个需要考虑的问题就是在降低storage overhead的同时降低reconstruction cost。

![](https://i.imgsafe.org/7df72bd865.png)



## parameter of erasure code

RS code的参数选择的过程就是一个trade-off的过程

![](https://i.imgsafe.org/7e1ca00904.png)

# erasure code in Windows Azure Storage

微软的WAS提供的是存储服务，要容错自然需要冗余，他在erasure code上就进行了一个优化，提出了LRC（local reconstruction code）。下面进行介绍。

## Windows Azure Storage (WAS)

WAS是一个存储系统，但它有一个特性，就是append-only，已经写好的数据是不会改的，因此很适合用erasure code来降低冗余。它的replication的unit称为extents，当extents数据大小没到3GB的时候，使用3-replication，当到达3GB之后就seal，并用erasure code计算冗余，算好之后，就删除掉replicas。

## Conventional Erasure Coding in WAS

使用RS（6，3）是6份数据，3份冗余，storage overhead是（6+3）/6=1.5

使用RS（12，4）是12分数据，4份冗余，overhead是（12+4）/12=1.33,虽然降低了冗余，但是reconstruction cost确由6到了12。

这就是LRC的目的，在降低overhead的基础上还能使reconstruction cost处于一个较低值。

## Local Reconstruction Code in LRC

LRC（12+2+2）是指12份数据，2份local冗余，2份global冗余

### reliability of LRC

LRC reliability的计算可以通过markov模型，LRC（12+2+2）可以解决任意3个failure

