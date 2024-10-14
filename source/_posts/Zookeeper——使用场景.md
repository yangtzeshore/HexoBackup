---
title: Zookeeper——使用场景
date: 2020-10-30 21:35:20
tags:
- 
categories:
- Zookeeper 
---

# Zookeeper——使用场景

### 前言

  这篇文章取自[这篇博客](https://github.com/doocs/advanced-java/blob/master/docs/distributed-system/zookeeper-application-scenarios.md)，作为整理和收录，方便后续查阅。

  大致来说，zookeeper 的使用场景如下，举几个简单的：

- 分布式协调
- 分布式锁
- 元数据/配置信息管理
- HA 高可用性

### 分布式协调

  这个其实是 zookeeper 很经典的一个用法，简单来说，就好比，你 A 系统发送个请求到 mq，然后 B 系统消息消费之后处理了。那 A 系统如何知道 B 系统的处理结果？用 zookeeper 就可以实现分布式系统之间的协调工作。A 系统发送请求之后可以在 zookeeper 上**对某个节点的值注册个监听器**，一旦 B 系统处理完了就修改 zookeeper 那个节点的值，A 系统立马就可以收到通知，完美解决。

[![zookeeper-distributed-coordination](https://raw.githubusercontent.com/yangtzeshore/images/main/Zookeeper/zookeeper-distributed-coordination.png)](https://yangtzeshore.github.io/2020/10/30/Zookeeper-使用场景/)

### 分布式锁

  举个栗子。对某一个数据连续发出两个修改操作，两台机器同时收到了请求，但是只能一台机器先执行完另外一个机器再执行。那么此时就可以使用 zookeeper 分布式锁，一个机器接收到了请求之后先获取 zookeeper 上的一把分布式锁，就是可以去创建一个 znode，接着执行操作；然后另外一个机器也**尝试去创建**那个 znode，结果发现自己创建不了，因为被别人创建了，那只能等着，等第一个机器执行完了自己再执行。

[![zookeeper-distributed-lock-demo](https://raw.githubusercontent.com/yangtzeshore/images/main/Zookeeper/zookeeper-distributed-lock-demo.png)](https://yangtzeshore.github.io/2020/10/30/Zookeeper-使用场景/)

### 元数据/配置信息管理

  zookeeper 可以用作很多系统的配置信息的管理，比如 kafka、storm 等等很多分布式系统都会选用 zookeeper 来做一些元数据、配置信息的管理，包括 dubbo 注册中心不也支持 zookeeper 么？

[![zookeeper-meta-data-manage](https://raw.githubusercontent.com/yangtzeshore/images/main/Zookeeper/zookeeper-meta-data-manage.png)](https://yangtzeshore.github.io/2020/10/30/Zookeeper-使用场景/)

### HA 高可用性

  这个应该是很常见的，比如 hadoop、hdfs、yarn 等很多大数据系统，都选择基于 zookeeper 来开发 HA 高可用机制，就是一个**重要进程一般会做主备**两个，主进程挂了立马通过 zookeeper 感知到切换到备用进程。

[![zookeeper-active-standby](https://raw.githubusercontent.com/yangtzeshore/images/main/Zookeeper/zookeeper-active-standby.png)](https://yangtzeshore.github.io/2020/10/30/Zookeeper-使用场景/)

### 参考文章

[1. zookeeper-application-scenarios](https://github.com/doocs/advanced-java/blob/master/docs/distributed-system/zookeeper-application-scenarios.md)