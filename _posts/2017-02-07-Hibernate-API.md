---
layout: post
title:  "Hibernate-API"
date:   2017-02-06
excerpt: "本篇介绍了Hibernate的API，包括Configuration、SessionFactory、Session、Transaction、Query、Criteria"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

><a href="#1">Configuration</a>      
><a href="#2">SessionFactory</a>   
><a href="#3">Session</a>  
><a href="#4">Transaction</a>  
><a href="#5">Query</a>  
><a href="#6">Criteria</a>  

***  

<a name="1"></a>

## <center>Configuration:负责管理Hibernate的配置信息</center>


### 1.加载核心配置文件

核心配置有两种即hibernate.properties与hibernate.cfg.xml。  

1.加载hibernate.properties:  
Configuration configuration = new Configuration();
{: .notice}

2.加载hibernate.cfg.xml:  
Configuration configuration = new Configuration().`configure();`
{: .notice}

### 2.加载映射文件

需要通过Configuration来加载映射文件的情况有两种：  

* 没有办法在属性配置文件hibernate.properties中加载映射文件(属性配置文件不能加载映射，需要手动编码)  
* hibernate.cfg.xml配置中没有加载映射

1.第一种写法:导入一个指定位置的映射文件  
configuration.addResource("cn/xsw/hibernate3/Customer.hbm.xml");
{: .notice}

2.第二种写法:导入与类同一个包下的映射文件  
configuration.addClass(Customer.class);  
注：要求映射文件名称的前缀必须与类名相同，类与映射在同一个包下，因为会通过类找配置文件。    
比如Customer类所在包下的Customer.hbm.xml。
{: .notice}

***

<a name="2"></a>

## <center>SessionFactory:Session工厂</center>

Configuration对象根据当前的配置信息生成SessionFactory对象。  
SessionFactory对象中保存了当前的数据库配置信息和所有映射关系以及预定义的SQL语句。  
SessionFactory对象是线程安全的。  
SessionFactory还负责维护Hibernate的二级缓存。
{: .notice}

### 抽取工具类

SessionFactory对象根据数据库信息，维护连接池，创建Session(相当于Connection)对象。  
构造SessionFactory很消耗资源，一般情况一个应用只初始化一个。  
可以抽取HibernateUtils用来提供Session对象：  

```java
//抽取工具类:
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class HibernateUtils {
	private static Configuration configuration;
	private static SessionFactory sessionFactory;
	
	static{
		configuration = new Configuration().configure();
		sessionFactory = configuration.buildSessionFactory();
	}
	
	public static Session openSession(){
		return sessionFactory.openSession();
	}
	
	public static void main(String[] args) {
		openSession(); //创建表
	}
}
```

```java
public class HibernateTest {
	@Test
	public void demo(){
		Session session = HibernateUtils.openSession();
		Transaction tx = session.beginTransaction();
		
		Customer customer = new Customer();
		customer.setName("tom");
		customer.setAge(26);
		
		session.save(customer);
		
		tx.commit();
		session.close();
		//不需要把连接池关掉
	}
}
```

### c3p0连接池

在Hibernate中使用c3p0连接池:
	
1.引入c3p0的jar包(位置:optional-c3p0)  	
2.在核心配置中添加一段配置:

```xml
<!-- C3P0连接池设定-->
<!-- 使用c3po连接池  配置连接池提供的供应商-->
<property name="connection.provider_class">org.hibernate.connection.C3P0ConnectionProvider</property>
<!--在连接池中可用的数据库连接的最少数目 -->
<property name="c3p0.min_size">5</property>
<!--在连接池中所有数据库连接的最大数目  -->
<property name="c3p0.max_size">20</property>
<!--设定数据库连接的过期时间，以秒为单位，
如果连接池中的某个数据库连接处于空闲状态的时间超过了timeout时间，就会从连接池中清除 -->
<property name="c3p0.timeout">120</property>
 <!--每3000秒检查所有连接池中的空闲连接，以秒为单位-->
<property name="c3p0.idle_test_period">3000</property>
```

***

<a name="3"></a>

## <center>Session</center>

1.相当于JDBC的Connection。  
2.Session是线程不安全的。  
3.Session是应用程序与数据库之间交互操作的一个单线程对象，是Hibernate运作的中心。  
4.所有持久化对象必须在Session的管理下才可以进行持久化操作(即增删改查)。  
5.Session对象有一个一级缓存，显式执行flush之前，所有的持久化操作的数据都缓存在Session对象处。  
6.持久化类与Session关联起来后就具有了持久化的能力。  
7.Session维护了Hiberante一级缓存。
{: .notice}

### 常用方法

```
save()/persist()	:添加(persist是jpa提供)
update()		:修改
saveOrUpdate() 		:增加或修改对象(根据数据库中是否有与之对应的记录)
delete()		:删除对象
get()/load()  		:根据主键查询
createQuery() 		:创建一个Query接口，编写HQL语句
createSQLQuery() 	:创建一个SQLQuery接口，编写SQL语句数据库操作对象
createCriteria()  	:返回一个Criteria接口，条件查询
```


***

<a name="4"></a>

## <center>Transaction</center>


### 获得

`Transaction tx = session.beginTransaction();`

### 常用方法

```
commit();	 :提交相关联的session实例
rollback();	 :撤销事务操作
wasCommitted();	 :检查事务是否提交
```

注1：如果没有开启事务，那么每个Session的操作，都相当于一个独立的事务。比如：  
Serializable id = session.save(customer);  
session.get(Customer.class, id);  
这两个操作是在两个不同的事务中。  
(默认回滚，没有提交，数据库中没有发生变化。)
{: .notice}

注2：如果在配置中开启了默认提交:  
`<propertiy name="hibernate.connection.autocommit">true</propertiy>`   
则即使不写Transaction tx = session.beginTransaction();和tx.commit();  
也会提交数据库，但执行的语句是独立的，若还有session的其它操作，则它们并不在一个事务里。  
所以一般配置文件中都设置自动提交为false。
{: .notice}

***

<a name="5"></a>

## <center>Query</center>

Query代表面向对象的一个Hibernate查询操作。  
session.createQuery 接收一个HQL语句。
HQL是Hibernate Query Language缩写，语法很像SQL语法，但是完全面向对象的。  

### 使用Query对象步骤

获得Hibernate的Session对象  
编写HQL语句  
调用session.createQuery 创建查询对象  
如果HQL语句包含参数，则调用Query的setXXX设置参数  
调用Query对象的list()或uniqueResult()方法执行查询
{: .notice}


### Query还包含两个方法用于控制返回结果

setFirstResult(int firstResult) 设置返回结果从第几条开始  
setMaxResults(int maxResults) 设置本次返回结果记录条数
{: .notice}


```java
public void demo(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	// 1.简单查询
	List<Customer> list = session.createQuery("from Customer").list();
	
	// 2.条件查询:
	List<Customer> list = session.createQuery("from Customer where name = ?")
				     .setParameter(0, "芙蓉")
				     .list();
	// 3.分页查询:select * from customer limit a,b; (a:从哪开始 b:每页显示记录数)
	Query query = session.createQuery("from Customer");
	query.setFirstResult(3); // a
	query.setMaxResults(3); // b
	
	List<Customer> list = query.list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
	tx.commit();
	session.close();
}
```

***

<a name="6"></a>

## <center>Criteria</center>

Criteria是Hibernate提供的用于条件查询接口。
HQL符合数据库的操作形式，而Criteria符合面向对象方式。  
即HQL语句类似SQL，QBC语句更类似对象操作。   

### 使用Criteria对象步骤

获得Hibernate的Session对象  
通过Session获得Criteria对象  
使用Restrictions的静态方法创建Criterion条件对象  
向Criteria对象中添加Criterion 查询条件  
执行Criterita的list()或uniqueResult()获得结果
{: .notice}

```java
@Test
// QBC:
public void demo(){

	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	// 1.简单查询
	List<Customer> list = session.createCriteria(Customer.class).list();
	
	// 2.条件查询:
	Criteria criteria = session.createCriteria(Customer.class);
	criteria.add(Restrictions.eq("name","芙蓉"));
	List<Customer> list = criteria.list();
	
	// 3.分页查询:
	Criteria criteria = session.createCriteria(Customer.class);
	criteria.setFirstResult(3);
	criteria.setMaxResults(3);
	List<Customer> list = criteria.list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
	
	tx.commit();
	session.close();
}
```

