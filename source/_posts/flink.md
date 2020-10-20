---
title: Flink 学习笔记 - Flink 简单介绍
categories: Flink
photos: 'https://flink.apache.org/img/flink-header-logo.svg'
date: 2020-09-23 22:25:55
---

Flink 是近年来越来越火的一款开源大数据计算引擎，它同时支持批处理和流处理。

<!-- more -->

# 有界流和无界流

任何类型的数据都数据流处理，信用卡交易记录、传感器测量数据、机器日志、网站或移动应用程序上的用户交互记录。。。

- **无界流**定义了起点，但没有定义终点，称为流处理。

- **有界流**定义了流的开始和结束，称为批处理。

![img](https://flink.apache.org/img/bounded-unbounded.png)

# 有状态与无状态计算

**有状态的计算**：每次计算基于之前的计算结果（状态）进行计算，并且每次计算结果都会保存到存储介质中，计算关联上下文。基于有状态的计算不需要将历史数据重新计算，提高了计算效率
**无状态的计算**：每次进行计算只考虑当前数据，不会使用之前的计算结果

# Flink 的特点和优势

1、同时支持高吞吐、低延迟、高性能
2、支持事件时间（Event Time）概念，结合 Watermark 处理乱序数据
3、支持有状态计算，并且支持多种状态   内存、文件、RocksDB
4、支持高度灵活的窗口（Window）操作   time、count、session
5、基于轻量级分布式快照（CheckPoint）实现的容错 保证恰好一次（exactly-once） 语义
6、基于 JVM 实现独立的内存管理
7、Save Points（保存点）

# 谁在使用 Flink

- AWS
- ebay
- Alibaba
- Tencent
- oppo
- 小米
- 唯品会
- 滴滴
。。。