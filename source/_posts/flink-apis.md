---
title: Flink 学习笔记 - API 介绍
tags: Flink
photos: 'https://flink.apache.org/img/flink-header-logo.svg'
date: 2020-10-01 09:25:55
---

Flink 为流处理/批处理应用程序的开发提供了不同级别的抽象。

<!-- more -->

![](https://ci.apache.org/projects/flink/flink-docs-release-1.11/fig/levels_of_abstraction.svg)

# 有状态实时流处理
Flink API 最底层的抽象为有状态实时流处理。其抽象实现是 Process Function，并且 Process Function 被 Flink 框架集成到了 DataStream API 中来为我们使用。
它允许用户在应用程序中自由地处理来自单流或多流的事件（数据），并提供具有全局一致性和容错保障的状态。
此外，用户可以在此层抽象中注册事件时间（event time）和处理时间（processing time）回调方法，从而允许程序可以实现复杂计算。

# 