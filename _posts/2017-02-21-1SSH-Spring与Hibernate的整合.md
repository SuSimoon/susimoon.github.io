---
layout: post
title:  "SSH-Spring与Hibernate的整合"
date:   2017-02-21
excerpt: "本篇介绍了SSH框架中Spring与Hibernate整合的两种方式，HibernateTemplate的API以及在Web层开启事务"
tag:
- Java 
- SSH框架
- 整合
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">方式一：零障碍整合</a>  
><a href="#2">方式二：没有Hibernate配置文件的整合</a>   
><a href="#3">HibernateTemplate的API</a>   
><a href="#4">Web层开启事务-OpenSessionInView</a> 

***

<a name="1"></a>

## <center>方式一：零障碍整合</center> 

可以在Spring中引入Hibernate的配置文件。  


**1.通过LocalSessionFactoryBean在Spring中直接引用hibernate配置文件**
(注意版本是hibernate3)

```xml		
<!-- 零障碍整合:在Spring配置文件中引入hibernate的配置文件 -->
<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
	<property name="configLocation" value="classpath:hibernate.cfg.xml"/>
</bean>
```
    

**2.Spring提供了Hibernate的模板，只需将HibernateTemplate模板注入给DAO**

注入连接池对象，可以自动获得模板。  

```xml
<!-- DAO的配置 -->
<bean id="bookDao" class="cn.xsw.dao.BookDao">
	<property name="sessionFactory" ref="sessionFactory"/>
</bean>
```


**3.改写DAO:继承HibernateDaoSupport类**

HibernateDaoSupport内部创建了hibernate模板。  

```java
public class BookDao extends HibernateDaoSupport{  
	public void save(Book book) {
		this.getHibernateTemplate().save(book);
	}
}
```


**4.事务管理:**

```xml
<!-- 事务管理 -->
<bean id="transactionManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
	<!-- 连接池 -->
	<property name="sessionFactory" ref="sessionFactory"/>
</bean>

<!-- 注解开启事务 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```


***

<a name="2"></a>

## <center>方式二：没有Hibernate配置文件的整合</center> 

不需要Hibernate配置文件的方式，将Hibernate配置文件的信息直接配置到Spring中。  


Hibernate配置文件中的信息有:  
* 连接数据库基本参数  
* Hibernate常用属性  
* 连接池  
* 映射
{: .notice}


**1.连接池:**

```xml
<!-- 引入外部属性文件 -->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!-- 配置c3p0连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="${jdbc.driver}"/>
	<property name="jdbcUrl" value="${jdbc.url}"/>
	<property name="user" value="${jdbc.user}"/>
	<property name="password" value="${jdbc.password}"/>
</bean>
```

**2.Hibernate常用属性:**

```xml
<!-- 配置Hibernate的属性 -->
<property name="hibernateProperties">
	<props>
		<prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
		<prop key="hibernate.show_sql">true</prop>
		<prop key="hibernate.format_sql">true</prop>
		<prop key="hibernate.hbm2ddl.auto">update</prop>
		<prop key="hibernate.connection.autocommit">false</prop>
	</props>
</property>
```

**3.映射:**

```xml
<property name="mappingResources">
<list>
	<value>cn/xsw/vo/Book.hbm.xml</value>
</list>
</property> 

<!--或:(可以直接写映射文件所在的文件夹)-->
<property name="mappingDirectoryLocations">
	<list>
		<value>classpath:cn/xsw/vo</value>
	</list>
</property>
```

### 程序示例：没有Hibernate配置文件

```xml
<!-- 连接池信息 -->
<!-- 引入外部属性文件 -->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!-- 配置c3p0连接池 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="${jdbc.driver}"/>
	<property name="jdbcUrl" value="${jdbc.url}"/>
	<property name="user" value="${jdbc.user}"/>
	<property name="password" value="${jdbc.password}"/>
</bean>

<!-- 配置连接池的信息 -->
<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
	<!-- 注入连接池 -->
	<property name="dataSource" ref="dataSource"/>
	<!-- 配置Hibernate的属性 -->
	<property name="hibernateProperties">
		<props>
			<prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
			<prop key="hibernate.show_sql">true</prop>
			<prop key="hibernate.format_sql">true</prop>
			<prop key="hibernate.hbm2ddl.auto">update</prop>
			<prop key="hibernate.connection.autocommit">false</prop>
		</props>
	</property>
	
	<!-- 加载映射 -->
	<!-- <property name="mappingResources">
		<list>
			<value>cn/xsw/vo/Book.hbm.xml</value>
		</list>
	</property> -->

	<property name="mappingDirectoryLocations">
		<list>
			<value>classpath:cn/xsw/vo</value>
		</list>
	</property>
</bean>
```

***

<a name="3"></a>

## <center>HibernateTemplate的API</center> 


```java
Serializable save(Object entity)    			//保存数据
void update(Object entity) 				//修改数据
void delete(Object entity) 				//删除数据
<T> T get(Class<T> entityClass, Serializable id) 	//根据ID进行检索，立即检索
<T> T load(Class<T> entityClass, Serializable id) 	//根据ID进行检索，延迟检索

List find(String queryString, Object... values) 	//支持HQL查询，直接返回List集合
List findByCriteria(DetachedCriteria criteria)  	//离线条件查询
List findByNamedQuery(String queryName, Object... values)//命名查询的方式
```

***

<a name="4"></a>

## <center>Web层开启事务-OpenSessionInView</center> 

如下程序访问action时会出现异常，原因是事务开启在业务层，  
当web层使用book对象时事务已经关闭，因此book对象成了脱管对象。  
解决办法:在web.xml中配置OpenSessionInView，会在web层开启事务。
{: .notice}

{% capture images %}
	http://ww4.sinaimg.cn/large/83e1667djw1f9yt5zyh7tj21rc0hs41e.jpg
{% endcapture %}
{% include gallery images=images cols=1 %}


```java
//Dao
public Book findByIdLazy(Integer id){ //延迟加载
	return this.getHibernateTemplate().load(Book.class,id);
}

//Service
public Book findByIdLazy(Integer id){
	return bookDao.findByIdLazy(id);
}

//Action
public String findByIdLazy(){
	Book book = bookService.findByIdLazy(2);
	System.out.println(book);
	return NONE;
}

```

**解决办法:**

```xml
<filter>
	<filter-name>OpenSessionInViewFilter</filter-name>
	<filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
</filter>

<filter-mapping>
	<filter-name>OpenSessionInViewFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```


