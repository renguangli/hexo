---
title: zookeeper 集群搭建
categories: Java
date: 2020-10-20 12:41:45
---

zookeeper 集群搭建

使用 3 台机器搭建 zookeeper 集群

<!-- more -->

# 基础环境装备

## 机器准备

使用三台机器搭建 zookeeper 集群

| 服务器 IP     | 主机名 |
| ------------- | ------ |
| 192.168.10.16 | node01 |
| 192.168.10.17 | node02 |
| 192.168.10.18 | node03 |

## 关闭防火墙

将三台机器的防火墙关闭，并关闭开机自启

```bash
[root@node01 /]# systemctl stop firewalld
[root@node01 /]# systemctl disable firewalld
```

## 配置 hosts

配置三台机器的 hosts

```bash
[root@node01 /]# vi /etc/hosts
192.168.10.16 node01
192.168.10.17 node02
192.168.10.18 node03
```

## 安装 JDK

JDK 安装请参考 [Linux 下安装 JDK](https://guangli.ren/jdk-install.html)

JDK 安装路径为 /opt/jdk/jdk1.8.0_241

![](/images/这个问题我们不会.jpg)

# 下载 zookeeper

下载地址 <https://archive.apache.org/dist/zookeeper/zookeeper-3.4.13/zookeeper-3.4.13.tar.gz>

这里下载的是 3.4.13 版本

下载完成后，将安装包上传到 node01 节点的 /opt 目录下

/opt 目录没有的话，先创建，三个节点都要创建

```bash
[root@node01 /]# mkdir /opt
```

安装包上传完成后，解压

```bash
[root@node01 opt]# tar -xf zookeeper-3.4.13.tar.gz
```

# 配置环境变量

三个节点都要配置

```bash
[root@node01 /]# vi /etc/profile
export JAVA_HOME=/opt/jdk/jdk1.8.0_241
export ZOOKEEPER_HOME=/opt/zookeeper-3.4.13
export PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin
[root@node01 /]# source /etc/profile
```

# 修改配置文件

```bash
[root@node01 zookeeper]# cd /opt/zookeeper-3.4.13/conf/ 
[root@node01 conf]# cp zoo_sample.cfg zoo.cfg
# 修改数据目录，默认是 /tmp/zookeeper
dataDir=/data/zookeeper 
# 在文件末尾添加节点信息
server.1=node01:2888:3888
server.2=node02:2888:3888
server.3=node03:2888:3888
```
/data/zookeeper 目录不存在的话需要在三台节点上先创建

```bash
[root@node01 conf]# mkdir -p /data/zookeeper
```

server.n 这个 n 就是 myid 文件的内容

将 zookeeper 安装包分发到另两个节点上

```bash
[root@node01 opt]# scp -r zookeeper-3.4.13/ node02:`pwd`
[root@node01 opt]# scp -r zookeeper-3.4.13/ node03:`pwd`
```

# 创建 myid 文件

在每台节点的数据目录里创建 myid 文件，并将对应的 server.n 写入 myid 文件中

比如 server.1=node01:2888:3888 这样配置，将 1 写到 node01 节点的 myid 文件中
```bash
[root@node01 opt]# echo 1 > /data/zookeeper/myid
```
server.2=node02:2888:3888 这样配置，将 2 写到 node02 节点的 myid 文件中
```bash
[root@node02 opt]# echo 2 > /data/zookeeper/myid
```
server.3=node03:2888:3888 这样配置，将 3 写到 node03 节点的 myid 文件中
```bash
[root@node03 opt]# echo 3 > /data/zookeeper/myid
```

# 启动、验证

在三台节点上分别启动 zookeeper

我们配置了环境变量，可以在任何目录使用 zookeeper 的 shell 启动脚本

node01 节点启动 zookeeper

```bash
[root@node01 opt]# zkServer.sh start
```
node02 节点启动 zookeeper
```bash
[root@node02 opt]# zkServer.sh start
```
node03 节点启动 zookeeper
```bash
[root@node03 opt]# zkServer.sh start
```

查看状态

```bash
[root@node01 opt]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper-3.4.13/bin/../conf/zoo.cfg
Mode: follower
```

```bash
[root@node02 opt]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper-3.4.13/bin/../conf/zoo.cfg
Mode: leader
```
```bash
[root@node03 opt]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper-3.4.13/bin/../conf/zoo.cfg
Mode: follower
```

node02 为 leader 节点，node01 和 node03 为 follower 节点

至此，zookeeper 集群搭建

# 简单使用

```bash
[root@node01 opt]# zkCli.sh
[zk: localhost:2181(CONNECTED) 0] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 1] create /test test
Node already exists: /test
[zk: localhost:2181(CONNECTED) 2] get /test
cZxid = 0x400000004
ctime = Tue Oct 20 11:34:23 CST 2020
mZxid = 0x400000004
mtime = Tue Oct 20 11:34:23 CST 2020
pZxid = 0x400000004
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 0
```