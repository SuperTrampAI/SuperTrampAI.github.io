---
title: SpringBoot+RabbitMQ
categories: Coder
date: 2019-06-02 19:02:01
tags: RabbitMQ
description:
copyright:
---
RabbitMQ开源消息代理软件（或称为面向消息的中间件），实现了高级消息队列协议（AMQP：Adanced Mssage Queuing Protocol）
<!-- more -->

# RabbitMQ相关概念详解：
消息队列是一种应用间的通信方式。实现了系统之间的双向解耦，生产者大量生产数据，消费者无法快速消费，通过一个中间层，来保存这个数据，生产者无需知道消费者的存在，反之依然。
生产者：发消息的程序
队列
交换机：根据自定义的规则进行消息的转发
消费者
虚拟主机：虚拟主机的存在在于实现消息的权限控制，一个虚拟主机持有特定的一组信息，包括交换机，队列和绑定。
绑定：交换机和队列绑定

RabbitMQ服务支持的操作系统：
	1. Linux
	2. Windows
	3. macOS
	4. Solaris
	5. FreeBSD
	6. TRU64
	7. VxWorks

RabbitMQ支持下列编程语言：
	1. Python
	2. java
	3. Ruby
	4. PHP
	5. javaScript
	6. Go
	7. Elixir
	8. Objective-C
	9. Swift
	10. C#

使用：
先安装Erlang（因为RabbitMQ使用Rrlang语言开发） 而后安装RabbitMQ
分别在如下地址下载Erlang和RabbitMQ
[Erlang](http://www.erlang.org/downloads)
使用命令安装RabbitMQ可视化插件：在命令行："F:\Learn\RabbitMQ\rabbitmq_server-3.7.15\sbin\rabbitmq-plugins.bat" enable rabbitmq_management，并使用命令重启服务：net stop RabbitMQ && net start RabbitMQ
而后就可以通过地址访问：http://localhost:15672/
[RabbitMQ]( https://www.rabbitmq.com/download.html)

Doing:
	1. 下载RabbitMQ可视化插件："F:\Learn\RabbitMQ\rabbitmq_server-3.7.15\sbin\rabbitmq-plugins.bat" enable rabbitmq_management （双引号里面的为你安装路径）
	2. 安装完以后，需要使用管理员运行CMD，重启RabbitMQ：net stop RabbitMQ && net start RabbitMQ
	3. 然后配置RabbitMQ用户：用户名，密码，权限，创建后，可能会有延迟或者是其他：不能马上在可视化界面中拿刚刚创建的RaabbitMQ登录，可视化地址为： http://localhost:15672/，如下操作均在安装目录下path\sbin下

常用命令：
~~~
查看已有用户：rabbitmqctl.bat list_users
新增用户：rabbitmqctl.bat add_user username password
为用户添加标签(可以指定多个tags)：rabbitmqctl.bat set_user_tags username tags[administration]（monitoring,policymaker,management）
 更改密码：rabbitmqctl.bat  change_passwork username newpassword
删除用户：rabbitmqctl.bat delete_user username
更改用户权限：
~~~


交换机发送消息的四种方式：
	1. Direct：direct 类型的行为是”先匹配, 再投送”. 即在绑定时设定一个 routing_key, 消息的routing_key 匹配时, 才会被交换器投送到绑定的队列中去. 例子：routing key标记为“user”的消息，只会转发想匹配的消息，不会转发其他的消息
	2. Topic：按规则转发消息（最灵活）：使用点分割，有两个通配符*：匹配一个词,"#"：匹配多个词
	3. Headers：设置 header attribute 参数类型的交换机
	4. Fanout：转发消息到所有绑定队列

# 踩过的坑
问题描述：RabbitMQ队列中，没有对应的队列，
[Cannot prepare queue for listener. Either the queue doesn't exist or the broker will not allow us to use it.](https://juejin.im/entry/5a6d64c2f265da3e4f0a6889)
