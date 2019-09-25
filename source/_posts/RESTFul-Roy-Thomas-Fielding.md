---
title: RESTFul - Roy Thomas Fielding
categories: Coder
date: 2019-05-08 14:04:00
tags: REST
description: My work is motivated by the desire to understand and evaluate the architectural design of network-based application software through principled use of architectural constraints, thereby obtaining the functional, performance, and social properties desired of an architecture.

---
REST由Roy Thomas Fielding博士在他的博士论文中提出来，论文地址：[REST](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)

在开篇提到：
本文研究计算机科学两大前沿,软件和网络,的交叉点。长期以来，软件研究主要关注软件设计的分类、设计方法的演化，很少客观地评估不同的设计选择对系统行为的影响。而相反地，网络研究主要关注系统之间通信行为的细节、如何改进特定通信机制的表现，常常忽视了一个事实，那就是改变应用程序的互动风格比改变互动协议，对整体表现有更大的影响。我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。

This dissertation explores a junction on the frontiers of two research disciplines in computer science: software and networking. Software research has long been concerned with the categorization of software designs and the development of design methodologies, but has rarely been able to objectively evaluate the impact of various design choices on system behavior. Networking research, in contrast, is focused on the details of generic communication behavior between systems and improving the performance of particular communication techniques, often ignoring the fact that changing the interaction style of an application can have more impact on performance than the communication protocols used for that interaction. My work is motivated by the desire to understand and evaluate the architectural design of network-based application software through principled use of architectural constraints, thereby obtaining the functional, performance, and social properties desired of an architecture.

也就是期望解决的问题是：如何把软件架构和网络结合在一起，形成一个两者兼容的解决方案。

RESTful API的无状态的优势
REST Web服务需要扩展以满足日益提高的性能要求。具有负载平衡和故障转移功能、代理和网关的服务器集群通常以形成服务拓扑的方式进行组织，从而允许根据需要将请求从一个服务器路由到另一个服务器，以减少Web服务调用的总体响应时间。要使用中间服务器扩大规模，REST Web服务需要发送完整、独立的请求；也就是说，发送的请求包括所有需要满足的数据，以便中间服务器中的组件能够进行转发、路由和负载平衡，而不需要在请求之间在本地保存任何状态。
完整、独立的请求不要求服务器在处理请求时检索任何类型的应用程序上下文或状态。REST Web 服务应用程序（或客户端）在HTTP Header和请求正文中包括服务器端组件生成响应所需要的所有参数、上下文和数据。这种意义上的无状态可以改进Web服务性能，并简化服务器端组件的设计和实现，因为服务器上没有状态，从而消除了与外部应用程序同步会话数据的需要。

概念详解：
REST Representational State Transfer（表现层状态转化）

基于网络的软件架构访问资源，就如你访问一个具体的网页，具有一个唯一的url。同理，在rest中，客户端请求服务器，也是定义了一个唯一的url来访问资源。

表现层：资源的展现形式，可以是txt，html，xml，json，jgp，png等
url便是资源的位置，展现的形式在HTTPP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。
如下：
~~~~
Content-Type：application/json; charset=UTF-8
Accept：pplication/json
Accept-Encoding（可选）：gzip
~~~~
状态转化：访问一个网站，势必是和网站的后台实现数据的交互，你的每一次行为，代表着数据的变化，而通讯协议是HTTP，有四种操作方式：GET，POST，PUT，DELETE，对于的和服务端的四种交互方式。

总结为一句话：客户端通过唯一的url（名词）访问服务端，通过四个HTTP动词实现数据的交互。

REST期望达成的目的
从资源寻址的客户端应用程序的角度看，URI决定了Rest Web服务将具有的直观程度，以及服务是否将以设计者能够预测的方式被使用。
rest服务url的直观性应该达到很容易猜测的程度，可以将url开作是自身配备的文档说明，只需要很少的资料或者解释，即可了解它指向什么，并获得相关资源。

如何设计能够使rest的url达到这一点？

1.url中的字母都是小写
2.将空格替换为下划线
3.尽可能多地避免查询字符串
4.URL描述的是一个特定资源，因此描述需要名词，不能出现动词。因为动词描述的不再是资源本身，而是行为。
5.利用HTTP请求的动词表示对资源操作的行为。
6.对于资源的描述的名词应该是层级嵌套的方式，比如/company/department/projects。通过这种对于信息层级描述的方式，更利于实体的抽象，以及增加客户端与服务器端开发人员对于整个系统模块认识的一致性。
路径终点的命名考虑用复数形式，比如/books。一般一个URL路径表示的资源会映射为数据库一系列表的记录的集合，因此使用复数更直观。POST方法URL路径不能含有参数。
~~~~
1.GET方法    从服务器取出资源(一项或多项)
2.POST方法    在服务器新建一个资源
3.PUT方法    在服务器更新资源（客户端提供改变后的完整资源）
4.PATCH方法    在服务器更新资源（客户端提供改变的属性）
5.DELETE方法    从服务器删除资源
~~~~
例子：
~~~~
1.GET  /books （获取所有书列表）
2.GET  /books/1 (获取编号为1的书)
3.GET  /books/1/summary (获取编号为1的书目的简介信息)
4.POST  /books （增加一本书）
5.PUT  /books/1 （修改编号为1的书）
6.DELETE  /books/2 （删除编号2的书）
对父子类的访问：
7。服务器端的组合实体必须在URI中通过父实体的id导航访问。
栗子：组合实体不是first-class的实体，它的生命周期完全依赖父实体，无法独立存在，在实现上通常是对数据库表中某些列的抽象，不直接对应表，也无id。一个常见的例子是 User — Address，Address是对User表中zipCode/country/city三个字段的简单抽象，无法独立于User存在。必须通过User索引到Address： GET /user/1/addresses。
~~~~
反面栗子：
过深的导航容易导致URL膨胀，不易维护，如:GET/zoos/1/areas/3/animals/4

正面栗子:使用参数的方式替代
GET /animals？zoo=1&area=3
