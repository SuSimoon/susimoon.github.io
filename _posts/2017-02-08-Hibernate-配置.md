---
layout: post
title:  "Hibernate-配置"
date:   2017-02-08
excerpt: "本篇介绍了Hibernate的配置，包括核心配置与映射配置"
tag:
- Java 
- Hibernate
- 配置
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

><a href="#1">核心配置</a>    
><a href="#2">映射配置</a>  

***

<a name="1"></a>

## <center>核心配置</center>


### 方式一：属性文件的配置

文件名：hibernate.properties  
格式：`key=value`  
例如：hibernate.connection.driver_class=com.mysql.jdbc.Driver  
注意：没有办法在核心配置文件中加载映射文件(必须手动编码的方式进行加载)。
{: .notice}

### 方式二：XML格式文件配置

文件名：hibernate.cfg.xml  
格式：`<property name="hibernate.connection.username">root</property>`
{: .notice}

### 必须的配置

**1.连接数据库4个基本参数:**

hibernate.connection.driver_class   连接数据库驱动程序  
hibernate.connection.url   			连接数据库URL  
hibernate.connection.username  		数据库用户名  
hibernate.connection.password  		数据库密码
{: .notice}

**2.Hibernate的方言:**
	
hibernate.dialect   操作数据库方言
{: .notice}

### 可选的配置:

hibernate.show_sql  true		        在控制台上输出SQL语句    
hibernate.format_sql  true   		    格式化控制台输出的SQL语句    
hibernate.connection.autocommit  true   事务是否自动提交    
hibernate.hbm2ddl.auto	create/create-drop/update/validate
{: .notice}

**hibernate.hbm2ddl.auto的四种取值：**

|         |        |
|:--------|:-------|
| **create**   | 每次执行时，创建一个新的表| 
|              | 如果以前有该表，将该表删除重新创建 |
|              | 一般测试的时候的使用  |
|:----
|**create-drop**| 每次执行时，创建一个新的表  | 
|              | 程序执行结束后将这个表删除掉 | 
|              | 一般测试的时候的使用  |
|              | 注：需要调用sessionFactory.close();才会删除 |
|:----
| **update**   | 如果数据库中没有表，创建一个新的表   | 
|              | 如果已有表，直接使用这个表 | 
|              | 可以更新表的结构(例如有新的属性时增加一列) | 
|:----
| **validate** | 会使用原有的表，完成校验  | 
|              | 校验映射文件与表中配置的字段是否一致，不一致报错  | 
|:--------|:-------|
{: rules="groups"}
***

<a name="2"></a>

## <center>映射配置</center>

### 映射文件的配置


• 配置Java对象与表映射:  
`<class name="cn.xsw.hibernate3.demo.Order" table=”orders”>`  

name:类的全路径    
table:表的名称  
注：table可以省略，会使用类的名称作为表名、table不区分大小写，class区分
{: .notice}

• 配置唯一标识与主键映射:  

一个表中只有一个主键的形式: `<id name=”id” column=”id”>`  
一个表对应多个主键形式(复合主键): `<composite-id></composite-id>`
{: .notice}

• 配置普通属性与字段映射:  
`<property name="name" column="name" type="string" length=”20”/>`  

|  type的三种写法：|
|:--------|:-------|
| 1.Java类型 | java.lang.String |
|:----|:----|  
| 2.Hibernate类型 |  string |
|:----|:----|
| 3.SQL类型(很少使用) |  需要子标签<column>|
|:--------|:-------|
{: rules="groups"}
{: .notice}


```xml
<property name="name">
	<column name="name" sql-type="varchar(20)"/>
</property>
```

• 命名SQL:  
直接通过名字获得HQL和SQL的命令语句，写在`<class>`标签之外  

```xml
<query name="findAll">
	from Customer
</query>

<sql-query name="sqlFindAll">
	select * from customer
</sql-query>
```

• 关联关系:

```xml  
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
	<!-- 建立类与表的映射 -->
	<!-- class标签:用于映射类与表的关系  name:类的全路径  table:表名称 -->
	<class name="cn.xsw.hibernate3.demo.Customer" table="customer">
		<!-- 建立类中属性与表中的字段映射 -->		
		
		<!-- 1.唯一标识 -->
		<!-- 使用id的标签 配置唯一属性 -->	
		<id name="id" column="id">
			<!-- 在<id>标签中配置一个主键的生成策略 -->
			<generator class="native"/>
		</id>
		

		<!-- 2.普通属性 -->
		<!-- property标签:映射类中的普通属性 name:类中的属性名称，column:表中字段名称 -->
		<property name="name" column="name" type="string"/>
		<property name="age" column="age"/>
	</class>
</hibernate-mapping>
```



### 映射文加载方式

1.在核心配置文件中加载映射文件:  
`<mapping resource="cn/xsw/hibernate/Customer.hbm.xml" />`  

2.使用手动编码的方式进行加载  
参考API章节。






