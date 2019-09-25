---
title: Spring Boot相关
categories: default
date: 2019-04-23 20:25:37
tags:
description:
---
Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建
以及开发过程。
<!-- more -->

#### spring是什么
具体来说Spring是一个轻量级的容器，用于管理业务相关对象的。核心功能主要为：IOC,AOP,MVC。
IOC：控制反转，将对象的创建过程交给容器，让容器管理对象的生命周期如创建，初始化，销毁等。
AOP：面向切面编程，对关注点进行模块化，通过对某一功能点进行编程，比如记录日志，有很多个
类都需要记录日志的方法，则创建记录日志的代理方法，需要调用该功能是只需要调用代理方法，
这就是AOP。MVC:SpringMvc,Spring提供的基于MVC模式设计的Web框架，如今比较流行的框架之一。

Spring太麻烦，Spring Boot横空出世。

Spring Boot 解决了什么问题：简化了Spring构件项目的复杂性，简化配置，简化编码。

#### Spring Boot 的常用注解
@Controller 将该类标注为控制层，控制层是视图层和业务层的桥梁，负责接收前台的请求，并且将
后台处理数据传送给前台。
@Service 将该类标注为业务层，开发人员可以在这一层编写业务逻辑，而不需要更多的关注如何
与数据库进行交互。
@Component 将该类定义类DAO层，负责与数据库进行数据交互。
@RequestMapping 匹配前台请求路径，可以标注在类之上，也可以标注在方法上面，然后类与方法进行
路径拼接。
@Resource 对象的注入，可以将容器创建的对象直接注入到需要的类中。@Autowired是按照类型注入，
而@Resource的装配顺序是如果指定名称或者没有指定时，按照名称装配；如果指定类型时，按照类型
装配。
@ResponseBody 这个注解很好用，是将后台需要返回的Map、list、字符串或者对象自动封装成json格式，
但是后台需要引入对应的jar包，jackson-all.jar、jackson-mapping.jar等。
@Transactional 这个注解标注方法为事务，保持操作的原子性，如果成功就提交；如果抛出异常，
就回滚。但是在容器的配置文件中需要配置事务。

#### spring boot相关问题
  1. 创建spring boot project的方式？答：在idea上面创建，在网站创建以后导入
  2. Spring Boot 可以兼容老 Spring 项目吗，如何做？答： 可以兼容，使用 @ImportResource 注解导入老 Spring 项目配置文件。  
  3. Spring Boot 如何定义多套不同环境配置？答：提供多套配置文件，在主配置文件中调用不同的配置文件
  4. SpringBoot 实现热部署有哪几种方式？答：引用devtools；自定义配置热部署
  5. Spring Boot 支持哪些日志框架？答：推荐和默认的日志框架是哪个？默认会使用Logback
  记录日志，并用info级别输出到控制台，也支持log4j，logging主流的日志实现。
  6. 你如何理解 Spring Boot 中的 Starters？依赖管理是任何复杂项目的关键部分。
  以手动的方式来实现依赖管理不太现实，你得花更多时间，同时你在项目的其他重要方面能付出的
  时间就会变得越少。Spring Boot starter 就是为了解决这个问题而诞生的。
  7. Spring Boot 自动配置原理是什么？Spring Boot的自动配置背后全依赖于@Conditional注解来实现的。
  8. 运行 Spring Boot 有哪几种方式？IDE 运行Application这个类的main方法；
  在springboot的应用的根目录下运行mvn spring-boot:run；使用mvn install 生成jar后运行
  9. Spring Boot 需要独立的容器运行吗？内置了tomcat ；jetty等容器。
  10. Spring Boot 的配置文件有哪几种格式？它们有什么区别？
  答：application.properties；application.yml
  11. spring的事务管理：什么是事务？
  答：Spring boot实现事务的方式：编程式事务和声明式事务（推荐使用这个声明事务的方式）。
  12. mvc工作流程：用户发起请求到核心控制器，收到请求调用对应的mapping映射器，
  然后找到具体的映射器以及拦截器，返回给控制器，而后调用适配器，具体的处理器，
  处理完后返回mobleAndView。解析并熏染。
  13. starter 是 Spring Boot 的一个重要组成部分，用于限制您需要执行的手动配置依赖项数量。
  要想有效地使用 Spring Boot，您应该了解 starter。starter 实际上是一组依赖项
  （比如 Maven POM），这些依赖项是 starter 所表示的应用程序类型所独有的。
