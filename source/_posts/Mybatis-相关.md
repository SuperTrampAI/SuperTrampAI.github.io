---
title: Mybatis 相关
categories: Coder
date: 2019-04-23 19:44:55
tags: Mybatis
description: MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射

---

### mybatis是什么
MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。
MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。
MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的
POJO为数据库中的记录。  

<!-- more -->

### 解决了什么问题
无论是Mybatis、Hibernate都是ORM的一种实现框架，都是对JDBC的一种封装.

### 技术的优点
易于掌握，对于sql的优化，管理方便，可以在xml内编写复杂的sql语句，动态sql.
### 技术的缺点
	1. 编写SQL语句时工作量很大，因为平台无关性弱，对应每一个数据库需要编写不同的原生sql
	2. 二级缓存机制不佳，容易产生脏数据
	3. xml标签要求必须唯一，导致接口无法重载
### 和其他技术的比较（有什么区别于其他技术）

Hibernate数据库移植性很好，MyBatis的数据库移植性不好，不同的数据库需要写不同SQL。
Hibernate有更好的二级缓存机制，可以使用第三方缓存。MyBatis本身提供的缓存机制不佳。
MyBatis可以进行更为细致的SQL优化，可以减少查询字段。MyBatis容易掌握，而Hibernate门槛较高。
Hibernate的DAO层开发比MyBatis简单，Mybatis需要维护SQL和结果映射。
Hibernate对对象的维护和缓存要比MyBatis好，对增删改查的对象的维护要方便。

> 任何的缺点和优点，都是事物的两面，在一个角度看过去是缺点的，在另外一个角度就是优点，
对于同一领域的两个框架，取长补短，是更为合适的做法，可以在修改操作是，利用Hibernate特性
，而在需要编写复杂查询时，利用mybatis的优势

开发工作量
Mybatis需要手动编写SQL语句，以及ResultMap。而Hibernate有良好的映射机制，
开发者无需关心SQL的生成与结果映射，可以更专注于业务流程。

### 技术的应用
${}和#{}
  #{}是预编译处理,解析传递进来的参数数据,以有效的防止SQL注入，提高系统安全性
  ${}是字符串替换,对传递进来的参数原样拼接在SQL中

### xml映射配置文件
    properties 语法：
    <!-- 定义 -->
		~~~
    <properties>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </properties>
    <!-- 使用 -- >
		~~~
		~~~
    <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>

    </dateSource>
~~~
    settings(会修改mybatis在运行时的行为方式)
		~~~
    <settings>
      <setting name="cacheEnabled" value="true"/>
      <setting name="lazyLoadingEnabled" value="true"/>
      <setting name="multipleResultSetsEnabled" value="true"/>
      <setting name="useColumnLabel" value="true"/>
      <setting name="useGeneratedKeys" value="false"/>
      <setting name="autoMappingBehavior" value="PARTIAL"/>
      <setting name="defaultExecutorType" value="SIMPLE"/>
      <setting name="defaultStatementTimeout" value="25"/>
      <setting name="safeRowBoundsEnabled" value="false"/>
      <setting name="mapUnderscoreToCamelCase" value="false"/>
      <setting name="localCacheScope" value="SESSION"/>
      <setting name="jdbcTypeForNull" value="OTHER"/>
      <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
    </settings>
    typeAliases:类型别名是为java类型命名一个短的名字。只用来减少类完全限定名的多余部分 语法：
        <typeAliases>
            <typeAlias alias="student" type="完整dao路径"></typeAlias>
        </typeAlises>  
				~~~
### 动态SQL
mybatis包含如下动态sql标签
trim|where|set|foreach|if|choose|when|otherwise|bind

语法：
~~~
<choose>
    <when>.......</when>
    <otherwise></otherwise>
</choose>
~~~

where语法：
~~~
<where>
    <if>.....</if>
    <if>.....</if>
    <if>.....</if>
</where>
~~~
如果where元素没有做出想要的，可以使用trim元素来自定义
~~~
<trim prefix="WHERE" prefixOverrides="AND | ON">...</trim>
~~~
set
语法：只用于update，set元素可以被用于动态包含更新的列，而不包含不需要更新的。
~~~
<set>
    <if test=""></if>
    <if test=""></if>
    <if test=""></if>
</set>
~~~

bind,可以在bind语句中定义一个键值对，比如可以定义一个模糊查询的字符串：
~~~
"'%' + _ parameter.getTitle() + '%'"，在select语句中使用键。比如：
<select id="selectBlogsLike" parameterType="Blog" resultType="Blog">
  <bind name="pattern" value="'%' + _ parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
~~~
接口绑定xml的方式：
一种是通过注解绑定,就是在接口的方法上面加上@Select@Update等注解里面包含Sql语句来绑定
另外一种就是通过xml里面写SQL来绑定,在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名.
