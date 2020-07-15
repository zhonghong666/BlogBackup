---
title: CAP定理
date: 2020-07-11 15:18:30
tags:
	- 分布式系统

---

> 分布式系统的最大难点，就是各个节点的状态如何同步。CAP 定理是这方面的基本定理，也是理解分布式系统的起点。

## 分布式系统的三个指标

![](https://www.wangbase.com/blogimg/asset/201807/bg2018071607.jpg)

1998年，加州大学的计算机科学家 Eric Brewer 提出，分布式系统有三个指标。

- Consistency：一致性
- Availability：可用性
- Partition Tolerance：分区容错性

Eric Brewer 说，这三个指标不可能同时做到。这个结论就叫做 CAP 定理。

