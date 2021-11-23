---
title: Redis 安装与简述
date: 2021-11-23 10:34:59
tags:
categories: 安装
---
> redis初认识

<!-- more -->
#### Redis 基本概念
> Redis结构介绍

```
1、Redis 是一个开源的使用 ANSI C 语言编写、遵守 BSD 协议、支持网络、可基于内存、分布式、可选持久性的键
值对(Key-Value)存储数据库，并提供多种语言的 API。
2、Redis 通常被称为数据结构服务器，因为值（value）可以是字符串(String)、哈希(Hash)、列表(list)、集
合(sets)和有序集合(sorted sets)等类型。
3、端口：6379
4、默认16个数据库，下标从0开始
5、单线程：redis是单线程+io多路复用：检查文件描述的就绪状态 ，Memchached：多线程+锁
```
> 相关资源

- [Redis 官网：https://redis.io/](https://redis.io/)
- [源码地址：https://github.com/redis/redis](https://github.com/redis/redis)
- [Redis 在线测试：http://try.redis.io/](http://try.redis.io/)
- [Redis 命令参考：http://doc.redisfans.com/](http://doc.redisfans.com/)


#### 使用场景
> 为什么使用

```
解决应用服务器的cpu和内存压力;
减少io的读操作，减轻io的压力;
关系型数据库的扩展性不强，难以改变表结构.
```

> 优点

```
nosql数据库没有关联关系，数据结构简单，拓展表比较容易;
nosql读取速度快，对较大数据处理快.
```
> 适用场景

```
数据高并发的读写;
海量数据的读写;
对扩展性要求高的数据.
```
> 不适场景

```
需要事务支持（非关系型数据库）;
基于sql结构化查询储存，关系复杂.
```
> 使用场景

```
配合关系型数据库做高速缓存 ，缓存高频次访问的数据，降低数据库io， 分布式架构，做session共享;
可以持久化特定数据。利用zset类型可以存储排行榜 利用list的自然时间排序存储最新n个数据.
```

#### 本地安装
> 本地下载和安装

```
brew install redis
```
> 安装目录

- Homebrew安装的软件会默认在/usr/local/Cellar/路径下
- redis的配置文件redis.conf存放在/usr/local/etc路径下

> 修改配置文件

- 默认是127.0.0.1 本地访问
- 默认没有密码，打开配置文件，找到pass，设置密码
- 客户端链接，端口号是6379，密码是刚才配置的密码，无需用户名

> 启动redis

```
brew service start redis
redis-server # 打开界面
```

> 启动go程序

- go mod tidy # 加载依赖
- run

```
备注：把redis快照强制关闭了导致不能持久化的问题，不能set。修改下面的配置
config set stop-writes-on-bgsave-error no
```

#### Redis实操
[go-redis相关资料：https://redis.uptrace.dev/](https://redis.uptrace.dev/)
> String（字符串）

```
set test "abcdefg" # 存储
get test # 取值
```

> Hash（哈希）: key-value

```
hmset hash_test key1 "value1" key2 "value2"
ghet hash_test key1
```

> List（列表:）简单的字符串列表,按照插入顺序排序

```
lpush list1 redis   #存储
lpush  list1 two  #存储
 lrange list1 0 10  #取值
```

> Set（集合） string 类型的无序集合

```
sadd set1 redis #存储
sadd set1 this #存储
sadd set1 is #存储
smembers set1 #取值
```

> zset(sorted set：有序集合)

```
zadd aset1 0 this  #存储
zadd zset1 1 is #存储
zadd zset1 2 a #存储
zrangebyscore zset1 0 10 #ls 取值
```

#### 服务器安装
```
1、下载，/home/work/source/
wget http://download.redis.io/releases/redis-5.0.7.tar.gz
2、解压
tar -zvxf redis-5.0.7.tar.gz
3、移动
mv redis-5.0.7 /usr/local/redis
4、编译，进入目录执行
make
5、安装:制定目录安装，后续删除 直接 rm就OK
make PREFIX=/usr/local/redis install
6、启动
./bin/redis-server& ./redis.conf
7、查看进程方式
ps -aux | grep redis
8、查看端口号
netstat -lanp | grep 6379
9、修改配置，让外网访问
注释掉bind 127.0.0.1这一行，这样可以使所有的ip访问redis
修改 protected-mode，值改为no，生产环境下要改为yes
为了Redis的安全，需要把#requirepass foobared去掉注释，foobared改为自己的密码，例如可以改为requirepass 123456
10、远程访问，ssh配置服务器的账号名和密码，端口是6379，如果在本地，那就直接用127.0.0.1和端口号就行了


```
