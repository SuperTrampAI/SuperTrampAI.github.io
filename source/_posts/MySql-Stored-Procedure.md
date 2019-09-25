---
title: MySql Stored Procedure
categories: Coder
date: 2019-04-25 17:30:03
tags: [MySql,Stored Procedure]
description: 存储过程是 SQL 语句和可选控制流语句的预编译集合
copyright: LiXiangHong
---

### 什么是存储过程

百度百科：存储过程(Stored Procedure),是一组为了完成特定功能的SQL 语句，集经编译后存储在数据库中，用户通过指定存储过程的名字并给出参数，如果该存储过程带有参数来执行。

维基百科： 储存程序 (Stored Procedure)，又可称预储程序或者存储过程，是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象，它可以视为数据库中的一种函数或子程序。
<!-- more -->

> google search 'mysql Stored Procedure'

> 可以完成特定的功能，在使用前编译并存放在数据库中，通过存储过程名来调用。
可以类比于C语言中的函数，通过函数名调用，传入参数，存储过程内部是逻辑。

可以把它理解为一个指令集，它可以帮助我们完成一系列复杂的数据操作，
也可以把它看做一个专门处理SQL的批处理工具，在需要的时候执行一些增删改查的操作。

> 存储过程是 SQL 语句和可选控制流语句的预编译集合

在业务变化不快的项目中，比如金融，企业，政府中，存储过程在商业数据库中应用的非常广泛，
存储过程一旦调试完成通过后就能稳定运行，这与各个业务在一段时间内是相对稳定和确定是匹配的；
存储过程大大地减少了业务系统与数据库的交互，一定程度降低了业务系统与数据库的耦合，
例如即使业务系统与应用系统不在同一城市，对性能的影响也可控
（100条SQL语句交互一次，即使延时由同城1ms增加到异地50ms，也只是增加49ms，如果交互100次，则增加4900ms）

优点：
1、重复使用。像函数一样调用，类比于java中的封装内部逻辑，从而可以减少数据库开发人员的工作量
（DBA使用的比较多，对于java程序员/后端开发相对应的使用较少）。可以重复利用的前提是在需求/表结构基本无变化的前提下。
2、减少网络流量。存储过程位于服务器上，调用的时候只需要传递存储过程的名称以及参数就可以了，因此降低了网络传输的数据量。
3、安全性。参数化的存储过程可以防止SQL注入式攻击，而且可以将Grant、Deny以及Revoke权限应用于存储过程。
4、效率高。由于数据库执行动作时，是先编译后执行的。然而存储过程是一个编译过的代码块，所以执行效率要比T-SQL语句高。

存储过程的缺点：
在mysql数据库中，存储过程的功能很弱，互联网行业的变化快，比如需求改变，改变表结构等，导致存储过程反而成为了鸡肋。
只能在单一的数据库中使用存储过程，无法迁移到其他数据库，每一家厂商提供的函数不一样。

### 存储过程的应用
~~~
DELIMITER $$
CREATE PROCEDURE proc1_select_count_bill(OUT s INT)
BEGIN
  SELECT COUNT(*) INTO s FROM bill;
END $$
DELIMITER ;
~~~
参数类型：IN,OUT,INOUT
IN：输入参数
表示该参数的值必须在调用存储过程时指定，在存储过程中修改该参数的值不能被返回，为默认值
OUT：输出参数
该值可在存储过程内部被改变，并可返回
INOUT：输入输出参数
调用时指定，并且可被改变和返回

举个栗子：
~~~
DELIMITER $$
CREATE PROCEDURE test_in_parameter(IN p_in INT)  
    BEGIN   
    DECLARE l_numeric number(8,2) DEFAULT 9.95;      
    SELECT p_in;   -- 1
    SET p_in=2;   
    SELECT p_in;   -- 2
    END$$
DELIMITER ;
~~~
~~~
SET @p_in=1;

CALL test_in_parameter(@p_in); -- 输出2   

SELECT @p_in;  输出 1 调用存储过程并不会改变值
~~~

~~~
DELIMITER  $$
CREATE PROCEDURE proc1 (IN parameter1 INTEGER)   
BEGIN   
   DECLARE variable1 CHAR(10);   
   IF parameter1 = 17 THEN   
       SET variable1 = 'birds';   
       ELSE
       SET variable1 = 'beasts';   
   END IF;   
INSERT INTO bill VALUES (variable1);  
END $$
DELIMITER ;
~~~
调用存储过程：
~~~
CALL procl(参数);
~~~
查看存储过程
~~~
SHOW PROCEDURE STATUS WHERE db='dom4';-- 查看数据库的所有存储过程
~~~
~~~
SHOW CREATE PROCEDURE dom4.`proc1`;-- 查看存储过程详细
~~~
用户变量
~~~
SELECT 'Hello World' INTO @x; -- SET @y='Goodbye Cruel World';   
SELECT @x;  
~~~
在存储过程中使用用户变量
~~~
CREATE PROCEDURE GreetWorld( ) SELECT CONCAT(@greeting,' World');
SET @greeting='Hello';  
CALL GreetWorld( );
~~~
在存储过程间传递全局范围的用户变量
~~~
CREATE PROCEDURE p1()   SET @last_procedure='p1';  
CREATE PROCEDURE p2() SELECT CONCAT('Last procedure was ',@last_procedure);  
CALL p1( );  
CALL p2( );  
~~~
使用用户变量应该注意：
1.用户变量名一般以@开头
2.滥用用户变量会导致程序难以理解及管理
删除存储过程
~~~
DROP PROCEDURE ProcedureName
~~~
if-then -else语句
~~~
DELIMITER $$
CREATE PROCEDURE procThen(IN parameter INT)  
  BEGIN
    DECLARE var INT;  
    SET var=parameter+
    IF var=0 THEN
        INSERT INTO t VALUES(1);  
    END IF;
    IF parameter=0 THEN
        UPDATE t SET s1=s1+
    ELSE
        UPDATE t SET s1=s1+
    END IF;  
  END $$
DELIMITER ;
~~~
case grammar：
~~~
DELIMITER $$
CREATE PROCEDURE procCase (IN parameter INT)  
  BEGIN
    DECLARE var INT;  
    SET var=parameter+1;  
    CASE var  
        WHEN 0 THEN   
          INSERT INTO t VALUES(1);  
        WHEN 1 THEN   
          INSERT INTO t VALUES(2);  
        ELSE   
          INSERT INTO t VALUES(3);  
    END CASE;  
  END $$
DELIMITER ;
~~~
while grammar
~~~
WHILE 条件 DO
-- 循环体
END WHILE
~~~
~~~
DELIMITER $$
  CREATE PROCEDURE procWhile()  
    BEGIN
    DECLARE var INT;  
    SET var=0;  
    WHILE var<5 DO  
      INSERT INTO t VALUES(var);  
      SET var=var+1;  
    END WHILE;  
  END $$
DELIMITER ;
~~~
repeat···· end repeat： 它在执行操作后检查结果，而while则是执行前进行检查。
（对应java中的do-while 和while()）
~~~
DELIMITER $$
CREATE PROCEDURE procRepeat ()  
  BEGIN   
    DECLARE value INT;  
    SET value=0;  
    REPEAT  
    INSERT INTO t VALUES(value);  
    SET value=value+1;  
    UNTIL value>=5  
    END REPEAT;  
END$$
DELIMITER ;  
~~~
loop ·····endloop grammar:
~~~
DELIMITER $$  
  CREATE PROCEDURE procLoop ()  
    BEGIN
      DECLARE value INT;  
      SET value=0;  
      LOOP_LABLE:LOOP  
        INSERT INTO t VALUES(value);  
        SET value=value+1;  
        IF value >=5 THEN
        LEAVE LOOP_LABLE;  
        END IF;  
      END LOOP;  
END $$
DELIMITER ;
~~~

> 参考：

[以及mysql自带的一些函数](https://www.jianshu.com/p/32bc449a1bf6)
[Stored Procedure](http://www.mysqltutorial.org/mysql-stored-procedure-tutorial.aspx)
[Stored Procedure](https://www.w3resource.com/mysql/mysql-procedure.php)
