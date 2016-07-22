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

MLFQ的核心思想是使用多个队列，每个队列具有不同的优先级，

- Rule1：优先级高的先执行
- Rule2：同一优先级的采用RR方式调度，执行自己分配到的时间片

 ![2016-07-22-9.46.15](https://i.imgsafe.org/17cff68421.png)

### Q1：优先级如何变化

- Rule3：默认刚进入系统的process先处于最高优先级
- Rule4a：如果一个任务用完分配的时间，优先级降低
- Rule4b：如果一个任务在用完时间之前yield，它保持在当前的优先级

#### example1：a single long-running job

![](https://i.imgsafe.org/17ff2d65f6.png)

可以看到job逐渐从最高优先级逐渐降低

#### example2：along came a short job

![](https://i.imgsafe.org/181159a95d.png)

从这个例子中可以看到，对于一个新来的job，我们不知道它是一个long job还是short job，我们先假设他是一个short job，如果是的话会很快执行完，不是的话，优先级会逐渐降低，从而接近SJF

#### example3：take io into consideration

假设有一个io intensive和一个cpu intensive的job

![](https://i.imgsafe.org/185ed6644d.png)

由于假设4b，io intensive的job会保持在最高的优先级

#### problem of our naive design

主要有两个问题

- starvation：如果有很多io intensive的job，那么他们会一直占用cpu，使得cpu intensive的job无法执行
- game the scheduler：如果一个job有意识的在快要结束的时候yield，就可以一直保持在较高的优先级了。

为了解决这些问题还要作进一步的改善。

### Q2：怎么解决starvation

- Rule5：在一段时间之后，所有的process都进入优先级最高的队列。

这样一来就可以解决两个问题：

- process不会starve
- 如果一个process前期是cpu intensive后期是io intensive，保证了后期会受到公正对待。

顺便一提，经历多长时间该boost所有的process呢，这个常量被称为voo-doo constants，要想确定下来它的合理的值需要一定的运气，甚至在不同的场景下会有不同的值是合理的。

### Q3：怎么防止game scheduler

- Rule4：不管一个process是否yield cpu，只要它用完分配的时间，优先级都会降低

### Summary

MLFQ可很好的应对不同的process具有不同运行时间，在一片未知的情况下，尽可能的提高平均的turnaround time，提高系统的交互性。

当MLFQ的问题是无法精确地表达这个算法的公平性，不同优先级具有不同的执行顺序，但有很多常值参数需要设定，难以保证稳定的调度效果。

## Proportional share scheduler

## Multiprocessor scheduling

# Linux Scheduler

# Xen credit scheduler













