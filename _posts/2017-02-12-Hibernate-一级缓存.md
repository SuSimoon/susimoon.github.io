---
layout: post
title:  "Hibernate-一级缓存"
date:   2017-02-12
excerpt: "本篇介绍了Hibernate中的一级缓存相关内容"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  


***

## <center>Hibernate的一级缓存</center>

缓存:缓存将数据库/硬盘上文件中数据，放入到缓存中(内存中一块空间)，当再次使用时可直接从内存中获取。  
缓存的好处:提升程序运行的效率。缓存技术是Hibernate的一个优化的手段。  

Hibernate分成两个基本的缓存:  

* 一级缓存: Session级别的缓存。一级缓存与session的生命周期一致，自带的，不可卸载。  
* 二级缓存: SessionFactory级别的缓存。不是自带的。
{: .notice}


### 一级缓存概述

* 在Session接口的实现中包含一系列的Java集合，这些Java集合构成了Session缓存。只要Session实例没有结束生命周期，存放在它缓存中的对象也不会结束生命周期。  
* 当session的save()方法持久化一个对象时，该对象被载入缓存，以后即使程序中不再引用该对象，只要缓存不清空，该对象仍然处于生命周期中。当试图get()、load()对象时，会判断缓存中是否存在该对象，有则返回，此时不查询数据库；没有再查询数据库。  
* Session能够在某些时间点，按照缓存中对象的变化来执行相关的SQL语句，来同步更新数据库，这一过程被称为刷出缓存(flush)。
{: .notice}



### 证明Hibernate一级缓存的存在

```java
@Test
// 证明一级缓存的存在
public void demo(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();		
	
	// 测试一：先save再get查询
	Book book1 = new Book();
	book.setName("JQuery开发");
	
	Integer id = (Integer) session.save(book1);
	Book book2 = (Book) session.get(Book.class, id); 
	System.out.println(book2);
	
	// 测试二：分别用get执行两次查询
	Book book3 = (Book) session.get(Book.class, 1); // 马上发生SQL去查询
	System.out.println(book1);
	
	Book book4 = (Book) session.get(Book.class, 1); // 不发生SQL，因为使用一级缓存的数据
	System.out.println(book2);
	
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
```


### 深入理解一级缓存中快照区

```java
@Test
// 深入理解一级缓存结构:快照区:
public void demo4(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();
	
	// 获得一个持久态的对象.
	Book book = (Book) session.get(Book.class, 1);
	book.setName("Spring3开发");
	
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
```

结论:  
向一级缓存存入数据的时候，放入一级缓存区和一级缓存快照区。  
当更新了一级缓存的数据的时候，事务一旦提交，比对一级缓存和快照区。  
如果数据一致，不更新，如果数据不一致，自动更新数据库。
{: .notice}

![](http://ww4.sinaimg.cn/large/83e1667djw1f9cc8hoh6zj21sa0okaip.jpg)


### Hibernate管理一级缓存

一级缓存是与session的生命周期相关的，session生命周期结束，一级缓存销毁了。

```
clear()			  清空一级缓存中所有的对象
evict(Object obj)	  清空一级缓存中某个对象
flush()			  刷出缓存
refresh(Object obj)       将快照区的数据重新覆盖一级缓存的数据
```

```java
@Test
// 一级缓存的管理:clear/evict();
public void demo5(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();
	
	Book book1 = (Book) session.get(Book.class, 1);
	Book book2 = (Book) session.get(Book.class, 2);
	System.out.println(book1);
	System.out.println(book2);
	
	// session.clear(); // 清空一级缓存的区域
	session.evict(book1); // 清空一级缓存的某个对象
	
	Book book3 = (Book) session.get(Book.class, 1);
	Book book4 = (Book) session.get(Book.class, 2);
	System.out.println(book3);
	System.out.println(book4);
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
```

```java
@Test
// 一级缓存的管理:flush()刷出缓存
public void demo6(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();
	
	Book book = (Book) session.get(Book.class, 1);
	book.setName("Hibernate3开发");
	
	session.flush();// 发出update语句 默认commit时发送update，但数据库中还没有更新，需要commit
	
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
```

```java
@Test
// 一级缓存的管理:refresh():将快照区的数据，覆盖一级缓存的数据
public void demo7(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();	
	
	Book book = (Book) session.get(Book.class, 1);
	book.setName("PHP开发");
	
	session.refresh(book);
	
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
```

### Hibernate一级缓存的刷出时机(了解)

```
FlushMode:
常量:
	* ALWAYS	每次查询的时候都会刷出、手动调用flush、事务提交的时候。
	* AUTO		默认值。有些查询会刷出、手动调用flush、事务提交的时候。
	* COMMIT	在事务提交的时候、手动调用flush的时候。
	* MANUAL	只有在手动调用flush才会刷出。

严格程度:MANUAL > COMMIT > AUTO > ALWAYS

session.setFlushMode(FlushMode.MANUAL);
```

