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



![https://i.imgsafe.org/7d87e01ca1.png]()

![https://i.imgsafe.org/7d88115b5d.png]()