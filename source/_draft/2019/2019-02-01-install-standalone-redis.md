---
layout: post
title: redis 单机版安装
---

redis 源码下载之后上传到 linux 上，或者在 linux `使用 wget` 工具下载

redis 下载地址：http://download.redis.io/releases/redis-5.0.8.tar.gz

``` bash
[root@localhost ~]# yum install wget # 安装 wget ，用于下载 redis 源码
[root@localhost ~]# yum install gcc # 安装 gcc 来编译 redis 源代码
[root@localhost ~]# wget http://download.redis.io/releases/redis-5.0.8.tar.gz # 下载 redis
[root@localhost ~]# tar xf redis-5.0.8.tar.gz # 解压
[root@localhost ~]# cd redis-6.0.5
[root@localhost ~]# make 
# 安装。PREFIX 参数指点装安装目录，没有会自动创建
[root@localhost redis-5.0.8]# make install PREFIX=/root/redis  
[root@localhost redis-5.0.8]# cp redis.conf /root/redis
```

## 前台启动

```bash
[root@localhost redis]# bin/redis-server redis.conf 
```

默认前台启动，也就是ctlr + c 后 redis-server 就会退出

## 后台启动

修改配置

```bash
vi redis.conf
daemonize no  ## no 改为 yes
```

daemonize 配置是来控制是否后台启动

修改完之后重新启动

```bash
[root@localhost redis]# bin/redis-server redis.conf
```

这样 redis 就是后台启动了

## 设置环境变量

```bash
[root@localhost]# vi /etc/profile
export REDIS_HOME=/root/redis
export PATH=$PATH:$REDIS_HOME/bin
[root@localhost]# source /etc/profile
```

设置环境变量是为了方便在任何地方使用 `redis-cli`

```bash
[root@localhost utils]# redis-cli
127.0.0.1:6379> set aaa bbb
OK
127.0.0.1:6379> get aaa
"bbb"
127.0.0.1:6379> keys *
1) "aaa"
127.0.0.1:6379> 
```



## 安装成 linux 服务

在 redis 源码目录下有个 utils 目录，这个目录下有一些工具，

其中就有将 redis 安装成 linux 服务的工具 install_server.sh

```bash
[root@localhost utils]# ./install_server.sh 
Welcome to the redis service installer
This script will help you easily set up a running redis server

Please select the redis port for this instance: [6379] 
Selecting default: 6379
Please select the redis config file name [/etc/redis/6379.conf] 
Selected default - /etc/redis/6379.conf
Please select the redis log file name [/var/log/redis_6379.log] 
Selected default - /var/log/redis_6379.log
Please select the data directory for this instance [/var/lib/redis/6379] 
Selected default - /var/lib/redis/6379
Please select the redis executable path [/root/redis/bin/redis-server] 
Selected config:
Port           : 6379
Config file    : /etc/redis/6379.conf
Log file       : /var/log/redis_6379.log
Data dir       : /var/lib/redis/6379
Executable     : /root/redis/bin/redis-server
Cli Executable : /root/redis/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort.
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
```

执行 `install_server` 脚本后会让你设置 端口号、配置文件路径、日志文件路径、数据目录、redis-server 路径

如果自己输入直接回车的话会选择默认配置

之后会再次确认配置是否正确，无误的回车即可，否则 `ctrl + c` 重新执行该脚本

回车后 脚本会将 redis 服务设置问题开启自启，运行级别在 345 

并且会帮你启动 redis

```bash
[root@localhost utils]# service redis_6379 status
Redis is running (19194)
```


