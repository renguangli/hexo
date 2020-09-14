---
title: nginx 丢失 nginx.pid 文件错误
photos: https://renguangli.gitee.io/images/nginx.png
date: 2018-05-13 17:22:54
tags: Nginx
category: Nginx
---

今天修改了下 nginx 配置，执行 `nginx -s reload` 命令后报了如下错误

``` bash
nginx: [error] open() "/run/nginx.pid" failed (2: No such file or directory)
```

<!-- more -->

`nginx -s reload` 命令需要通过 nginx.pid 获取 master 进程号，会去找 /var/run/nginx.pid ，

如果nginx.pid不存在就报 nginx.pid 文件找不到的错误

## 解决方法

### 杀掉 nginx 重新启动

找到 nginx 进程，然后杀掉，重启

``` bash
[root@localhost ~]# ps -ef|grep nginx
root     22919     1  0 20:06 ?        00:00:00 nginx: master process nginx
nginx    22920 22919  0 20:06 ?        00:00:00 nginx: worker process
root     22930 22819  0 20:06 pts/0    00:00:00 grep --color=auto nginx
[root@localhost ~]# kill -9 22919 22920
[root@localhost ~]# nginx
[root@localhost ~]#
```

### 新建nginx.pid

查看nginx主进程号

``` bash
[root@localhost ~]# ps -ef|grep nginx
root     22932     1  0 20:06 ?        00:00:00 nginx: master process nginx
nginx    22933 22932  0 20:06 ?        00:00:00 nginx: worker process
root     22943 22819  0 20:07 pts/0    00:00:00 grep --color=auto nginx
```

nginx主进程号为 22932

新建nginx.pid文件

``` bash
vi /var/run/nginx.pid
```

将主进程号写入nginx.pid文件

```bash
[root@localhost ~]# echo 22932 >  /var/run/nginx.pid
```

重新加载配置文件

``` bash
[root@localhost ~]# nginx -s reload
```

`nginx -s reload` 命令执行成功