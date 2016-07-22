---
layout: post
title: scheduler
date: 2016-7-21
categories: multi-core
tags: [总结,知识管理]
description: 最近看了Operating Systems Three Easy Pieces里面scheduler相关的部分，特此总结。
---

# Basic of scheduler

## 基本调度策略

os里面的scheduler要解决的问题是对于一个CPU，怎么确定在CPU上执行的proess，这可以称为调度。
调度的一般策略有以下一些，对于一个无所不知的调度器来说：

- FIFO first in first out
- SJF shortest job first
- STCF shortest time-to-compleon first
- RR round robin

这些调度策略就不详细介绍了。

## io处理

当一个process发生io的时候，会主动yield cpu，此时cpu将运行其他的process。
当一个process处理完成io之后，要么preempt当前在运行的process，要么等。

## MLFQ the multi-level feedback queue

接下来要解决两个最困难的问题，在不知道每个process运行时间的情况下：

- 降低turnaround(completion-arrival) time
- 使系统交互性更好(responsive to interactive users)

MLFQ的核心思想是使用多个队列，每个队列具有不同的优先级，优先级高的先执行。

 ![2016-07-22-9.46.15](https://i.imgsafe.org/17cff68421.png)



# Linux Scheduler

# Xen credit scheduler













