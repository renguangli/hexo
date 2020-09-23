---
title: Flink简单介绍
tags: Flink
photos: 'https://flink.apache.org/img/flink-header-logo.svg'
date: 2020-09-23 22:25:55
---

# 什么是 Flink

Flink 是近年来越来越火的一款开源大数据计算引擎，它同时支持批处理和流处理。

<!-- more -->

# 有界流和无界流

任何类型的数据都可以作为流产生，都可以作为无界流或有界流处理。

**无界流**定义了起点，但没有定义终点，称为流处理。

**有界流**具有定义流的开始和结束，有界流的处理也称为批处理。

![img](https://flink.apache.org/img/bounded-unbounded.png)

# 有状态与无状态计算

**有状态的计算**：每次进行数据计算的时候基于之前数据的计算结果（状态）做计算，并且每次计算结果都会保存到存储介质中，计算关联上下文 context

基于有状态的计算不需要将历史数据重新计算，提高了计算效率

**无状态的计算**：每次进行数据计算只是考虑当前数据，不会使用之前数据的计算结果

# Flink的特点和优势

1、同时支持高吞吐、低延迟、高性能
2、支持事件时间（Event Time）概念，结合Watermark处理乱序数据
3、支持有状态计算，并且支持多种状态   内存、文件、RocksDB
4、支持高度灵活的窗口（Window）操作   time、count、session
5、基于轻量级分布式快照（CheckPoint）实现的容错 保证exactly-once语义
6、基于JVM实现独立的内存管理
7、Save Points（保存点）