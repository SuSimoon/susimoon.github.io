---
layout: post
title:  "Spring-JdbcTemplate"
date:   2017-02-18
excerpt: "本篇介绍了Spring中JdbcTemplate的应用"
tag:
- Spring
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">Spring的JdbcTemplate</a>  
><a href="#2">配置连接池</a>   
><a href="#3">属性文件设置参数</a>   
><a href="#4">Dao继承JdbcDaoSupport</a>   
  
   

***

<a name="1"></a>

## <center>Spring的JdbcTemplate</center> 

JdbcTemplate模板与DbUtils工具类比较类似


### Spring对持久层技术支持


|    持久层技术支持   | 
|:-------- |:------- |
| JDBC  |org.springframework.jdbc.core.JdbcTemplate|
|:-------- |:------- |
| Hibernate3.0  |org.springframework.orm.hibernate3.HibernateTemplate|
|:-------- |:------- |
| IBatis(MyBatis) | org.springframework.orm.ibatis.SqlMapClientTemplate|
|:-------- |:------- |
|   JPA    | org.springframework.orm.jpa.JpaTemplate|
|:-------- |:------- |
{: rules="groups"}
{: .notice}


### 开发JDBCTemplate所需jar包

spring-tx-3.2.0.RELEASE.jar  
spring-jdbc-3.2.0.RELEASE.jar  
mysql驱动
{: .notice}

### 程序示例：

```java
@Test
public void demo(){
	// 创建连接池:
	DriverManagerDataSource dataSource = new DriverManagerDataSource();
	// 设置参数:
	dataSource.setDriverClassName("com.mysql.jdbc.Driver");
	dataSource.setUrl("jdbc:mysql:///spring");
	dataSource.setUsername("root");
	dataSource.setPassword("123");
	
	// 使用JDBC的模板:
	JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
	jdbcTemplate.execute("create table user (id int primary key auto_increment,name varchar(20))");
}
```

***

<a name="2"></a>

## <center>配置连接池</center> 


上述程序示例中，每次都要创建连接池并设置参数，过于繁琐。  
可以在applicationContext.xml中配置连接池。

**1.Spring默认的连接池:**

```xml
<!-- 配置Spring默认的连接池 -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql:///spring"/>
	<property name="username" value="root"/>
	<property name="password" value="123"/>
</bean>
```

**2.DBCP连接池:**
	
导入jar包:  
com.springsource.org.apache.commons.dbcp-1.2.2.osgi.jar  
com.springsource.org.apache.commons.pool-1.5.3.jar
{: .notice}

```xml
<!-- 配置DBCP连接池 -->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql:///spring"/>
	<property name="username" value="root"/>
	<property name="password" value="123"/>
</bean>
```

**3.C3P0连接池:**
	
导入jar包:  
com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar
{: .notice}

```xml		
<!-- 配置c3p0连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql:///spring"/>
	<property name="user" value="root"/>
	<property name="password" value="123"/>
</bean>
```

### 程序示例：

```xml
<!-- 配置c3p0连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql:///spring"/>
	<property name="user" value="root"/>
	<property name="password" value="123"/>
</bean>

<!-- 定义JDBCTemplate -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	<property name="dataSource" ref="dataSource"/>
</bean>

<bean id="userDao" class="cn.xsw.spring.UserDao">
	<!-- 注入JDBC模板 -->
	<property name="jdbcTemplate" ref="jdbcTemplate"/>
</bean>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringTest {
	@Autowired
	@Qualifier("jdbcTemplate")
	private JdbcTemplate jdbcTemplate;
	
	@Test
	public void demo(){
		jdbcTemplate.execute("create table user (id int primary key auto_increment,name varchar(20))");
	}
}
```

***

<a name="3"></a>

## <center>属性文件设置参数</center> 

在src下创建jdbc.properties(名字任意)：   

```properties
	jdbc.driver = com.mysql.jdbc.Driver(名字任意)
	jdbc.url = jdbc:mysql:///spring3_day02
	jdbc.user = root
	jdbc.password = 123
```

在applicationContext.xml中使用属性文件配置的内容：  

```xml
* 第一种写法:
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	<property name="location" value="classpath:jdbc.properties"></property>
</bean>

* 第二种写法:(推荐)
<context:property-placeholder location="classpath:jdbc.properties"/>
```

### 程序示例：

```xml
<!-- 要引入context的schema -->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!-- 配置c3p0连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="${jdbc.driver}"/>
	<property name="jdbcUrl" value="${jdbc.url}"/>
	<property name="user" value="${jdbc.user}"/>
	<property name="password" value="${jdbc.password}"/>
</bean>
```

***

<a name="4"></a>

## <center>Dao继承JdbcDaoSupport</center> 


### Spring框架中提供了对持久层技术支持的类:  

|    持久层支持的类   | 
|:-------- |:------- |
| JDBC     |org.springframework.jdbc.core.support.JdbcDaoSupport|
|:-------- |:------- |
| Hibernate3.0  |org.springframework.orm.hibernate3.support.HibernateDaoSupport|
|:-------- |:------- |
| IBatis(MyBatis) | org.springframework.orm.ibatis.support.SqlMapClientDaoSupport|
|:-------- |:------- |
{: rules="groups"}
{: .notice}

### 编写DAO的时候:

前面的例子都是在Dao中注入模板，但若有很多Dao需要编写，而每次都要注入很麻烦。    
可以采用继承JdbcDaoSupport的方式，其内部定义了模板，并提供了get、set方法。
{: .notice}

### 程序示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
	
	<context:property-placeholder location="classpath:jdbc.properties"/>
	
	<!-- 配置c3p0连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driver}"/>
		<property name="jdbcUrl" value="${jdbc.url}"/>
		<property name="user" value="${jdbc.user}"/>
		<property name="password" value="${jdbc.password}"/>
	</bean>
	
	<!-- 定义jdbctemplate -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"/>
	</bean>
	
	<bean id="userDao" class="cn.xsw.spring.UserDao">
		<property name="jdbcTemplate" ref="jdbcTemplate"/>
	</bean>
</beans>
```

```java
Public class UserDao extends JdbcDaoSupport {
	public void add(User user){
		String sql = "insert into user values (null,?)";
		this.getJdbcTemplate().update(sql, user.getName());
	}
}
```

