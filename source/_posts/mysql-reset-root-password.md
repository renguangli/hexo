---
title: MySQL 重置 root 用户密码
categories: MySQL
photos: 'https://renguangli.gitee.io/images/mysql-1.png'
date: 2020-04-02 16:18:42
---

如果忘记了 MySQL root 用户密码，如何重置？

<!-- more -->

# MySQL 重置 root 用户密码

## 添加配置 `skip-grant-tables`

``` bash
[localhost@root ~]# vi /etc/my.cnf
## 无密码登录
skip-grant-tables
[localhost@root ~]# service mysqld restart
```

## 使用客户端连接 MySQL 

```bash
[localhost@root ~]# mysql -uroot -p # 回车,不需要密码
```

## 修改 MySQL root 用户密码

5.7 版本之后已经没有了 `password` 字段，而是用 `authentication_string` 加密字段代替

```sql
update mysql.user set authentication_string=password('root') where user='root';
```

5.7 版本以前

```sql
update mysql.user set password=password('root') where user='root';
```

> 密码修改完成后，记得把配置 `skip-grant-tables` 去掉，然后重启 MySQL