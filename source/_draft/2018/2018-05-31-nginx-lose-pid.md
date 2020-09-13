---
layout: post
title: 'nginx: [error] open() "/var/run/nginx.pid" failed (2: No such file or directory)'

---


今天修改了下 nginx 配置，重新加载配置文件的时候报了如下错误
``` bash
[root@localhost ~]# nginx -s reload
nginx: [error] open() "/var/run/nginx.pid" failed (2: No such file or directory)
```
nginx.pid 文件丢了，也不知道什么原因，百度上都说需要kill掉重新启动nginx，但是我们的nginx不能重启。

我找到另一台机器发现 nginx.pid 文件里只是记录了nginx 的主进程号。我在`/var/run/`目录下新建了 nginx.pid 并
通过 `ps` 找到了nginx的主进程号写进了新建的 nginx.pid 文件里

``` bash
[root@localhost ~]# ps -ef|grep nginx
root      2484（主进程号）1  0 21:51 ?        00:00:00 nginx: master process nginx
nginx     2485  2484  0 21:51 ?        00:00:00 nginx: worker process
root      2507  2448  0 22:00 pts/0    00:00:00 grep --color=auto nginx

```
重新执行命令没有报错。
