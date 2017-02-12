---
layout: post
title:  "Hibernate-事务处理"
date:   2017-02-12
excerpt: "本篇介绍了Hibernate中"
tag:
- Java 
- Hibernate
- 事务
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">Hibernate中的事务</a>    
><a href="#2">丢失更新</a>  
><a href="#3">丢失更新的解决(悲观锁与乐观锁)</a>  
><a href="#4">Hibernate的检索(抓取)策略</a>  
><a href="#5">set集合上的检索策略(在一方对多方配置)</a>   
><a href="#6">many-to-one上的检索策略(在多方对一方配置)</a>   
><a href="#7">批量抓取 </a> 


***

<a name="1"></a>

## <center>Hibernate中的事务</center> 


### 事务

事务就是逻辑上的一组操作，要么全都成功，要么全都失败。
{: .notice}

### 事务特性

原子性:事务一组操作不可分割。  
一致性:事务的执行前后，数据完整性要保持一致。  
隔离性:一个事务在执行的过程中不应该受到其他事务的干扰。  
持久性:一旦事务结束，数据就永久保存数据库。
{: .notice}

### 不考虑事务的隔离性而引发的安全问题
不考虑事务的隔离性会引发5大类问题(3类读问题、2类写问题)。  

3类读问题:  

脏读	:一个事务读到另一个事务未提交数据。  
不可重复读:一个事务读到另一个事务已经提交数据(update)，导致查询结果不一致。  
虚读	:一个事务读到另一个事务已经提交的数据(insert)，导致查询结果不一致。
{: .notice}

2类写问题：

丢失更新
{: .notice}

### 避免三种读的问题的出现:

1.设置事务的隔离级别:

未提交读:以上三种读问题都有可能发生。  
已提交读:避免脏读，但是不可重复读和虚读有可能发生。  
重复读:避免脏读和不可重复读，但是虚读是有可能发生。  
串行的:可以避免以上三种读问题。
{: .notice}

2.在Hibernate中设置事务的隔离级别:  
在核心配置文件中:`<property name="hibernate.connection.isolation">4</property>`

1—Read uncommitted isolation  
2—Read committed isolation (oracle默认)  
4—Repeatable read isolation (mysql默认)  
8—Serializable isolation
{: .notice}


***

<a name="2"></a>

## <center>丢失更新</center>  

假设数据库中customer表的内容如下：  

|  cid     | cname   | age     |
|:--------:|:-------:|:-------:|
| 1        |   tom   |   25    |
|:-------- |:------- |:------- |
{: rules="groups"}
{: .notice}


当Demo1想去更新表中的cname并提交时，表中数据变为：

```java
Customer customer = (Customer) session.get(Customer.class, 3);
customer.setAge(30);
```

|  cid     | cname   | age     |
|:--------:|:-------:|:-------:|
| 1        |   tom   |   30    |
|:-------- |:------- |:------- |
{: rules="groups"}
{: .notice}

与此同时，Demo2想更新表中的cname，提交后表中数据后，Demo1更新的数据丢失：

```java
Customer customer = (Customer) session.get(Customer.class, 3);
customer.setCname("ann");
```

|  cid     | cname   | age     |
|:--------:|:-------:|:-------:|
| 1        |   ann   |   20    |
|:-------- |:------- |:------- |
{: rules="groups"}
{: .notice}

***
<a name="3"></a>

## <center>丢失更新的解决(悲观锁与乐观锁)</center>  

**1.悲观锁：**  

将记录锁定，其他人不能修改。

```java
// 使用悲观锁(排他锁)
Customer customer = (Customer) session.get(Customer.class, 3, LockMode.UPGRADE); 
// 此时的SQL语句末尾含有"for upgrade"
customer.setAge(30);
```

```java
Customer customer = (Customer) session.get(Customer.class, 3, LockMode.UPGRADE);
customer.setCname("ann"); //Debug时，当Demo1未完成修改时，此句无法执行，Demo1提交后才能执行
```

**2.乐观锁：**

//在Customer类中添加属性version:

```java
private Integer version;

```

同时在映射文件中添加属性version:

```xml
<version name="version"/>
<property name="cname" column="cname" length="20"/>
<property name="age" column="age"/>
```

此时表中添加了version字段并初始化为0：  

|  cid     | cname   | age     | version |
|:--------:|:-------:|:-------:|:-------:|
| 1        |   tom   |   25    |  0      |
|:-------- |:------- |:------- |:------- |
{: rules="groups"}
{: .notice}

当Demo1修改了年龄时，表中version字段自动变为1：

```java
Customer customer = (Customer) session.get(Customer.class, 1);
customer.setAge(30);
}
```

|  cid     | cname   | age     | version |
|:--------:|:-------:|:-------:|:-------:|
| 1        |   tom   |   30    |  1      |
|:-------- |:------- |:------- |:------- |
{: rules="groups"}
{: .notice}

因为Demo2中的的version字段是0，所以不能提交。  

```java
Customer customer = (Customer) session.get(Customer.class, 3);
customer.setCname("ann");
```

***

* 当前线程中的session

```
线程绑定的session:
	* 在Hibernate.cfg.xml中配置一个:
		<property name="hibernate.current_session_context_class">thread</property>
	* 使用SessionFactory中的getCurrentSession();方法。
		* 底层就是ThreadLocal

**** 当前线程中的session不需要进行关闭，线程结束后自动关闭。(写session.close()会报错)
```

![](http://ww2.sinaimg.cn/large/83e1667djw1f9g4vooebdj218q0o4448.jpg)

```xml
在核心配置文件中配置，session对象的生命周期与本地线程绑定
<!-- 使用当前线程中的session -->
<property name="hibernate.current_session_context_class">thread</property>
```

```java
public class HibernateUtils {
	private static final Configuration configuration;
	private static final SessionFactory sessionFactory;
	
	static {
		configuration = new Configuration().configure();
		sessionFactory = configuration.buildSessionFactory();
	}

	public static Session openSession(){
		return sessionFactory.openSession();
	}
	
	public static Session getCurrentSession(){ <-------
		return sessionFactory.getCurrentSession();
	}
	
	public static void main(String[] args) {
		openSession();
	}
}

```

```java
@Test
/*
 * 事务通常在service层开启.session在DAO层
 * 	* 事务开启由session开启
 */
public void demo7(){
	Session session1 = HibernateUtils.openSession(); 
	Session session2 = HibernateUtils.openSession();
	System.out.println(session1 == session2); // false
	
	Session session3 = HibernateUtils.getCurrentSession();
	Session session4 = HibernateUtils.getCurrentSession();
	System.out.println(session3 == session4); //true
}
```
