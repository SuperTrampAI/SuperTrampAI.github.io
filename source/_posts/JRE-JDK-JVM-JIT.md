---
title: JRE JDK JVM JIT
categories: Coder
date: 2019-05-04 09:52:05
tags: JDK
description: JRE JDK JVM JIT分别是什么
---

JRE JDK JVM JIT组成了java语言的独特性，以及优势
![](https://github.com/SuperTrampAI/ubiquitous-octo-system/blob/master/images/coder/jvm-jre-jdk1.png)
[图片来源](https://javapapers.com/)

<!-- more -->

#### Java Development Kit (JDK)
jdk包含jre
Java Development Kit (JDK) java开发工具包
包含各种开发工具，面向客户端程序员。
如果只是运行java程序，无需安装jdk，只需要按照jre即可。
JDK包含了一系列的组件：
~~~
javac：编译器，将后缀名为.java的源代码编译成后缀名为“.class”的字节码
java：运行工具，运行.class的字节码
jar：打包工具，将相关的类文件打包成一个文件
javadoc：文档生成器，从源码注释中提取文档，注释需匹配规范
jdb debugger：调试工具
jps：显示当前java程序运行的进程状态
javap：反编译程序
appletviewer：运行和调试applet程序的工具，不需要使用浏览器
javah：从Java类生成C头文件和C源文件。这些文件提供了连接胶合，使Java和C代码可进行交互。[2]
javaws：运行JNLP程序
extcheck：一个检测jar包冲突的工具
apt：注释处理工具[3]
jhat：java堆分析工具
jstack：栈跟踪程序
jstat：JVM检测统计工具
jstatd：jstat守护进程
jinfo：获取正在运行或崩溃的java程序配置信息
jmap：获取java进程内存映射信息
idlj：IDL-to-Java编译器。将IDL语言转化为java文件[4]
policytool：一个GUI的策略文件创建和管理工具
jrunscript：命令行脚本运行
~~~
#### Java Runtime Environment (JRE)
Java Runtime Environment (JRE) 运行时环境
jre包含jvm
jre（java运行时环境）是jvm的一种实现

#### Java Virtual Machine (JVM)
Java Virtual Machine (JVM) java虚拟机
JVM包含JIT
jvm（java虚拟机）抽象了计算机（硬件设备），实现了平台无关性。是jre 的一个实例，它被广泛称为运行时解释器。
JVM很大程度上有助于从使用JDK程序库的程序员中抽象内部实现。
在JRE JDK JVM JIT四者中，我们能够操作配置的是jvm。
如何调优配置？
** 当在命令提示符下启动并使用JVM时 **
1. 可以从cmd控制台进入j2sdk安装目录下的bin目录，运行java命令，会显示有相关配置属性。
使用java -X查看
然后使用javac HelloWorld.java
java -Xms256M -Xmx512M HelloWorld
这样的设置方式只能对当前运行的java文件产生作用。
** 当在集成开发环境下（如eclipse）启动并使用JVM时 **
![](https://github.com/SuperTrampAI/ubiquitous-octo-system/blob/master/images/coder/20190504112656.png)
2. 在eclipse根目录下打开eclipse.ini，默认内容为（这里设置的是运行当前开发工具的JVM内存分配）：
-vmargs-Xms40m-Xmx256m-vmargs表示以下为虚拟机设置参数，可修改其中的参数值，，另外，eclipse.ini内还可以设置非堆内存，如：-XX:PermSize=56m，-XX:MaxPermSize=128m。

此处设置的参数值可以通过以下配置在开发工具的状态栏显示：
在eclipse根目录下创建文件options，文件内容为：org.eclipse.ui/perf/showHeapStatus=true
修改eclipse根目录下的eclipse.ini文件，在开头处添加如下内容：
-debugoptions-vmjavaw.exe重新启动eclipse，就可以看到下方状态条多了JVM信息。
3. 打开eclipse－窗口－首选项－Java－已安装的JRE（对在当前开发环境中运行的java程序皆生效）
编辑当前使用的JRE，在缺省VM参数中输入：-Xmx128m-Xms64m-Xmn32m-Xss16m
4. 打开eclipse－运行－运行－Java应用程序（只对所设置的java类生效）
选定需设置内存分配的类－自变量，在VM自变量中输入：-Xmx128m-Xms64m-Xmn32m-Xss16m

注：如果在同一开发环境中同时进行了第二种和第三种设置，则第二种设置生效，第三种设置无效，如：
开发环境的设置为：-Xmx256m，而类Test的设置为：-Xmx128m-Xms64m，则运行Test时生效的设置为：
-Xmx256m-Xms64m
** 当在服务器环境下（如Tomcat）启动并使用JVM时（对当前服务器环境下所以Java程序生效） **
5. 设置环境变量：
变量名：CATALINA_OPTS
变量值：-Xmx128m-Xms64m-Xmn32m-Xss16m
6. 打开Tomcat根目录下的bin文件夹，编辑catalina.bat，将其中的%CATALINA_OPTS%（共有四处）替换为：-Xmx128m-Xms64m-Xmn32m-Xss16m
详细jvm参数自行Google[Google](https://www.google.com/)。
#### Just In Time compiler (JIT)
Just In Time compiler (JIT) 编译器
JIT 属于jvm的一部分，把字节码变成机器码来提高jvm的效率。是在运行时把字节码变成机器码，称为“动态编译”
