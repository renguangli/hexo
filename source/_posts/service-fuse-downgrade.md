---
title: 什么是熔断？什么是服务降级？
categories: Java
link_refer:
  - url: https://blog.csdn.net/pengjunlee/article/details/86688858
    title: 服务熔断与降级（Hystrix）
  - url: https://mp.weixin.qq.com/s?__biz=MzIxMjE5MTE1Nw==&mid=2653193370&idx=1&sn=60cc1478d7f44392a8d48b89132b6d1e&chksm=8c99f640bbee7f560f6e7ce685923676bb22774feab8acf522b81b2e668bf63bdcbf031b3983&mpshare=1&scene=1&srcid=0507B8nRBB1vwv42QLT3jTSJ#rd
    title: 漫画：什么是服务熔断？
date: 2020-10-06 22:25:06
---

# 什么是服务熔断？

熔断这一概念来源于电子工程中的断路器（CircuitBreaker）。服务熔断的作用类似于我们家用的保险丝，当某服务出现不可用或响应超时的情况时，上游服务为了保护系统整体的可用性，防止整个系统雪崩，暂时停止对该服务的调用。当检测到该服务调用正常后再恢复对该服务的调用。

<!-- more -->

这种牺牲局部，保全整体的措施就叫做熔断

# 什么是服务降级

服务降级是从整个系统的负荷情况出发和考虑的，为了防止某些服务出现负载过高或者响应慢的情况发生，上游服务暂时停止对非核心服务的调用，直接返回一个提前准备好的 fallback（退路）错误处理信息。这样虽然提供的是一个有损的服务，但是保证了整个系统的稳定性和可用性。

# 熔断 VS 降级

相同点
- 目标一致：都是从可用性和可靠性出发，为了防止系统崩溃；
- 用户体验类似：最终都让用户体验到的是某些功能暂时不可用；

不同点
- 触发原因不同：服务熔断一般是某个服务（下游服务）故障引起，而服务降级一般是从整体负荷考虑；

![](/images/起来改改.jpg)