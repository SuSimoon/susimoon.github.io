---
layout: post
title:  "Hibernate-概述与搭建"
date:   2017-02-06
excerpt: "本篇介绍了Hibernate框架的概述与环境搭建，以及Hibernate中使用的日志记录"
tag:
- Java 
- 概述与搭建
- Hibernate
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

><a href="#1">Hibernate概述</a>  
><a href="#2">Hibernate环境搭建</a>  
><a href="#3">Hibernate日志记录</a>

***

<a name="1"></a>

## <center>Hibernate概述</center>  

Hibernate是一个开放源代码的对象关系映射框架。  
它对JDBC进行了非常轻量级的对象封装，使得Java程序员可以使用**对象**编程思维来操纵数据库。

 
### 持久层的ORM的框架   
ORM:Object Relational Mapping 对象关系映射  
Hibernate就是一个持久层的ORM的框架。  

常见的持久层框架:    
　　* Hibernate  
　　* JPA(只有接口规范)   
　　* MyBatis: 前身是Ibatis  
　　* DbUtils
{: .notice}

### Hibernate的优点

1.Hibernate可以在应用EJB的J2EE架构中取代CMP，完成数据持久化的重任。   
2.Hibernate对JDBC访问数据库的代码做了封装，大大简化了数据访问层繁琐的重复性代码。  
3.Hibernate是一个基于JDBC的主流持久化框架，是优秀的ORM实现，很大程度的简化了dao层编码工作。  
4.Hibernate使用Java的反射机制，而不是字节码增强程序类实现透明性。  
5.Hibernate的性能非常好，因为它是一个轻量级框架。映射的灵活性很出色，它支持很多关系型数据库，从一对一到多对多的各种复杂关系。
{: .notice}



### Hibernate版本

Hibernate3.x版本和Hibernate4.x（企业中常用的还是Hibernate3.x）

***

<a name="2"></a>

## <center>Hibernate环境搭建</center> 


### 1.下载Hibernate的开发包

Hibernate框架目录结构：

![](http://wx1.sinaimg.cn/large/83e1667dgy1fcgr9trd0xj214o0l878r.jpg)


注：Hibernate的开发包下载地址为[http://sourceforge.net/projects/hibernate/files/hibernate3](http://sourceforge.net/projects/hibernate/files/hibernate3)
{: .notice}


### 2.导入相应jar包 


| 核心jar包 | hibernate3.jar | 
|----
| 必要的jar包 | home/lib/required/*.jar  |
|----
| jpa规范 | home/lib/jpa:hibernate-jpa-2.0-api-1.0.1.Final.jar|
|----
| 日志记录 | log4j-1.2.16.jar、slf4j-log4j12-1.7.2.jar |
|----
| 数据库驱动 |mysql-connector-java-5.0.4-bin.jar|
{: rules="groups"}

注：  
1.导入jar包时，要在Java工程中新建文件夹lib。  
2.Java工程中要把lib中的所有jar包右键Build Path -> add Build Path(Web工程不需要)  
3.将(日志文件夹中的)log4j.properties文件复制到src下。
{: .notice}

### 3.创建Hibernate的核心配置文件

• 引入约束:  

在src下创建hibernate.cfg.xml，引入dtd约束hibernate-confguration-3.0.dtd     
(或去project的etc里复制hibernate.cfg.xml)
{: .notice}

• 数据库属性:  

在etc下找到hibernate.properties去查找(mysql)数据库属性。
{: .notice}

```xml
<hibernate-configuration>
<session-factory>
	<!-- 1.必须去配置的属性 -->
	
	<!-- 配置数据库连接的基本信息: -->
	<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
	<property name="hibernate.connection.url">jdbc:mysql:///hibernate3</property>
	<property name="hibernate.connection.username">root</property>
	<property name="hibernate.connection.password">123</property>
	<!-- Hibernate的方言 -->
	<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>

	<!-- 2.可选的属性 -->
	
	<!-- 显示SQL -->
	<property name="hibernate.show_sql">true</property>
	<!-- 格式化SQL -->
	<property name="hibernate.format_sql">true</property>
	<!-- hbm2ddl:映射 to DDL:自动生成表 -->
	<property name="hibernate.hbm2ddl.auto">update</property>

	<!-- 3.通知Hibernate加载那些映射文件 -->
	(或可直接拖到configuration中的mappings中) 
	<mapping resource="cn/xsw/hibernate3/demo/Customer.hbm.xml" />
</session-factory>
</hibernate-configuration>
```


### 4.创建表(关系型数据库) 

```sql
create database hibernate3;
use hibernate3;
create table customer(
	id int primary key auto_increment,
	name varchar(20),
	age int
);
```


### 5.创建一个实体类(面向对象)  

```java
public class Customer {
	private int id;
	private String name;
	private int age;
	
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```


### 6.创建ORM的映射  


映射文件只要是一个XML格式文件就可以，名字任意。  
通常情况下名称规范: `实体类名称.hbm.xml` (hbm->hibernate mapping)
{: .notice}  

```xml 
<!--Customer.hbm.xml--> 
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
	<class name="cn.xsw.test.Customer" table="customer">	
		<!-- 1.唯一标识 -->
		<id name="id" column="id">
			<generator class="native"/>
		</id>
		
		<!-- 2.普通属性 -->
		<property name="name" column="name" type="string"/>
		<property name="age" column="age"/>
	</class>
</hibernate-mapping>
<!-- 注：table不区分大小写，class区分 -->
```


### 7.编写测试  

```java
@Test
// 向数据库中插入一条记录
public void demo(){
	// 1.Hiberante框架加载核心配置文件(解析)
	Configuration configuration = new Configuration().configure();
	// 2.创建一个SessionFactory(获得Session--相当于连接对象)
	SessionFactory sessionFactory = configuration.buildSessionFactory();
	// 3.由SessionFactory获得Session对象
	Session session = sessionFactory.openSession();
	// 4.默认的情况下，事务是不自动提交(开始一个事务)
	Transaction tx = session.beginTransaction();
	// 5.业务逻辑操作-向数据库中插入一条记录:
	Customer customer = new Customer();
	customer.setName("tom");
	customer.setAge(28);
	
	session.save(customer);
	
	// 6.事务提交
	tx.commit();
	// 7.释放资源
	session.close();
	sessionFactory.close();
}
```

***

<a name="3"></a>

## <center>Hibernate日志记录</center> 

Hibernate中的日志记录使用的是slf4j技术。  

### SLF4J  

SLF4J，即简单日志门面（Simple Logging Facade for Java）  
不是具体的日志解决方案，它只服务于各种各样的日志系统，用于整合其他日志系统。  

### Log4J

在企业中常用的日志记录:log4j，是具体的日志记录方案。  

**Log4J的日志级别**  

fatal(致命的)、error(普通错误)、warn(警告)、info(信息)、debug(调试)、trace(堆栈信息)  
	
**用法**  

`Logger.error(“错误信息”);`    
通过配置文件的形式，显示错误信息，假如配置的级别是info，则比info级别高的信息都会显示。

**Log4J的三个组件**

1.记录器（Loggers）

* 格式:记录器 = 级别, 输出源1,输出源2  
* log4j.rootLogger=info, stdout
{: .notice}

2.输出源（Appenders）

* log4j.appender.stdout=org.apache.log4j.ConsoleAppender:  控制台进行输出  
* log4j.appender.file=org.apache.log4j.FileAppender:       向文件进行输出
{: .notice}

3.布局（Layouts） 

* log4j.appender.stdout.layout=org.apache.log4j.PatternLayout  
* log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
{: .notice}


```properties
### direct log messages to stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.err
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### direct messages to file mylog.log ###
log4j.appender.file=org.apache.log4j.FileAppender
log4j.appender.file.File=/Users/SOoCoO/Documents/JavaWebFile/test/mylog.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### set log levels - for more verbose logging change 'info' to 'debug' ###

log4j.rootLogger=info, stdout,file

```


```properties
log4j.rootLogger=off, stdout
# 1.off表示控制台的输出无任何日志信息
# 2.若写成info，则表示info及比它级别高的信息会输出到控制台中

log4j.appender.file.File=/Users/SOoCoO/Documents/JavaWebFile/test/mylog.log
log4j.rootLogger=info, stdout,file
# 会在相应路径下生成mylog.log日志
```

**测试** 

```java
public class Log4JTest {
	private Logger logger = Logger.getLogger(Log4JTest.class);
	@Test
	public void demo(){
		logger.fatal("致命错误");
		logger.error("普通错误");
		logger.warn("警告信息");
		logger.info("普通信息");
		logger.debug("调试信息");
		logger.trace("堆栈信息");
	}
}
```
***

![](http://wx2.sinaimg.cn/large/83e1667dgy1fcoup014icj217e0p6djb.jpg)