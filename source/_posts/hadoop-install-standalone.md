---
title: hadoop 单机版安装
categories: [软件安装,hadoop]
link_refer:
  - url: https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
    title: https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
date: 2020-10-07 21:03:54
---

hadoop 单机版安装

<!-- more -->

# 安装环境

- OS: CentOS 7
- java: 1.8
- hadoop:2.9.2

# 基础环境准备

## 1.设置主机名

```
[root@hadoop ~]# vi /etc/hostname
hadoop
[root@hadoop ~]# hostname
hadoop
```

## 2.设置本机 ip 到主机名的映射

```
[root@hadoop ~]# vi /etc/hosts
192.168.10.20 hadoop
```

## 3.关闭防火墙

关闭防火墙并关闭开启自启

``` bash
[root@hadoop ~]# systemctl stop firewalld.service 
[root@hadoop ~]# systemctl disable firewalld.service 
```

## 4.免密登录

使用 `ssh-keygen` 命令生成私钥公钥
使用 `ssh-copy-id` 命令将公钥追加到目标主机j家目录 `~/.ssh/authorized_keys` 文件中

```
[root@hadoop ~]# ssh-keygen -t rsa -P '' 
[root@hadoop ~]# ssh-copy-id 192.168.10.20
```

## 5.时间同步

``` bash
[root@hadoop ~]# yum install ntp  -y
[root@hadoop ~]# vi /etc/ntp.conf
server ntp1.aliyun.com ## 阿里云时间同步服务器
[root@hadoop ~]# systemctl restart ntpd.service
[root@hadoop ~]# systemctl enable ntpd.service ##　设置开启自启
```

## 6.安装 jdk

JDK 安装请参考 [Linux 下安装 JDK](https://guangli.ren/jdk-install.html)

JDK 安装路径为 /opt/jdk/jdk1.8.0_241

![](/images/这个问题我们不会.jpg)

# 安装 hadoop

所有有关 ip 的配置使用主机名代替

## 1.配置环境变量

``` bash
[root@hadoop ~]# vi /etc/profile
export HADOOP_HOME=/opt/hadoop-2.9.2
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

## 2.hadoop-en.sh 

由于启动集群式需要使用 ssh 远程执行命令，导致找不到 JAVA_HOME,需要在 hadoop-en.sh Shell 脚本中配置 JAVA_HOME

``` bash
[root@hadoop bin]# cd $HADOOP_HOME/etc/hadoop
[root@hadoop hadoop]# vi hadoop-env.sh
export JAVA_HOME=/opt/jdk/jdk1.8.0_241
``` 

## 3.core.site.xml

配置 namedone 地址

```
[root@hadoop hadoop]# vi core-site.xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop:9000</value>
    </property>
</configuration>
```

## 4.hdsf-core.xml

配置 hdfs 副本数为 1，namenode/datanode/namesecondary 的数据目录以及 namesecondary 地址
namenode/datanode/namesecondary 的数据目录默认在 /tmp 下，随时能被删除
为了数据的可靠性修改为其他目录，我配置的目录为 /data/hadoop/dfs

```
[root@hadoop hadoop]# vi hdfs-site.xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/data/hadoop/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/data/hadoop/dfs/data</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop:50090</value>
    </property>
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>/data/hadoop/dfs/namesecondary</value>
    </property>
</configuration>
```

## 5.slaves

配置 datanode 节点

```
[root@hadoop hadoop]# vi slaves
hadoop
```


## 6.格式化 namenode 目录

```
hdfs namenode -format
```

## 7.启动 namenode 和 datanode

```
start-dfs.sh
```

# 简单使用

## 1.创建用户目录并查看

``` bash
[root@hadoop hadoop]# hdfs dfs -mkdir /user/root
[root@hadoop hadoop]# hdfs dfs -ls /user
Found 1 items
drwxr-xr-x   - root supergroup          0 2020-10-07 20:54 /user/root
```

## 2.上传 hadoop 安装包至 /user/root 目录下

``` bash
[root@hadoop opt]# hdfs dfs -put hadoop-2.9.2.tar.gz /user/root
[root@hadoop opt]# hdfs dfs -ls /user/root
Found 1 items
-rw-r--r--   1 root supergroup  366447449 2020-10-07 21:55 /user/root/hadoop-2.9.2.tar.gz
``` 

## 3.查看文件内容

``` bash
[root@hadoop hadoop]# hdfs dfs -put slaves
[root@hadoop hadoop]# hdfs dfs -cat /user/root/slaves
hadoop
```

## 4.删除 hadoop-2.9.2.tar.gz 文件

``` bash
[root@hadoop opt]# hdfs dfs -ls /user/root
Found 2 items
-rw-r--r--   1 root supergroup          7 2020-10-07 21:57 /user/root/slaves
-rw-r--r--   1 root supergroup  366447449 2020-10-07 21:55 /user/root/hadoop-2.9.2.tar.gz
[root@hadoop hadoop]# hdfs dfs -rm /user/root/hadoop-2.9.2.tar.gz
Deleted /user/root/hadoop-2.9.2.tar.gz
[root@hadoop hadoop]# hdfs dfs -ls /user/root
Found 1 items
-rw-r--r--   1 root supergroup          7 2020-10-07 21:57 /user/root/slaves
```

## 5.查看 hdfs WEBUI 页面

![](/images/hdfs-webui.png)