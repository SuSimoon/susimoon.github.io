---
layout: post
title:  "Hibernate-持久化"
date:   2017-02-08
excerpt: "本篇介绍了Hibernate中持久化类的编写规则、主键生成策略以及持久化对象的三种状态"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">持久化类编写规则</a>    
><a href="#2">自然主键和代理主键</a>  
><a href="#3">主键生成策略</a>  
><a href="#4">持久化对象的三种状态</a>  


***

<a name="1"></a>

## <center>持久化类编写规则</center>

Hibernate采用普通、传统的Java对象（POJO），作为持久化类，与数据表进行映射。  
持久化类: 实体类 + 映射文件  


**1.提供一个无参数、public访问控制符的构造器**

因为用到了反射
{: .notice}

**2.提供一个标识属性，映射数据表主键字段**

* Java区分两个对象是否是同一个:使用地址  
* 数据库区分两条记录是否一致:使用主键  
* Hibernate区分持久化对象是否是同一个:根据唯一标识OID  
(对象的OID和数据库的表的主键对应。为保证OID的唯一性，应该让Hibernate来为OID赋值)
{: .notice}

```java
@Test
// 演示关于标识错误:会抛出异常
public void demo(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Customer customer1 = (Customer) session.get(Customer.class, 10);
	
	Customer customer2 = new Customer();
	customer.setId(10);
	customer.setName("tom");
	session.update(customer2);
	
	tx.commit();
	session.close();
}
```


**3.所有属性提供public访问控制符的set、get方法**

框架中存值和取值的时候使用
{: .notice}

**4.标识属性应尽量使用基本数据类型的包装类型**

使用基本数据类型的例子:   
基本数据类型会将成绩初始化为0，不能确定是没考试还是0分，用包装类型则初始化为null。  

|成绩表：|
|:--------|:-------|:-------|
|学号     | 姓名     | 成绩 |
|:----   |:----     |:----|
|１      |tom       |0->null|
|:----   |:----     |:----|
{: rules="groups"}
{: .notice}

**5. 持久化类尽量不要使用final进行修饰**  

用final修饰的类是不能被继承，无法生成代理对象进行优化。  
(延迟加载的时候返回的是代理对象，延迟加载会失效)
{: .notice}

```java
public final class Customer {
	private int id;
	...

}
```

```java
@Test
// 演示持久化类为final情况:debu时测试时，延迟加载失败，生成的是真实对象而非代理对象customer
public void demo(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Customer customer = (Customer) session.load(Customer.class, 10);
	
	tx.commit();
	session.close();
}
```


***

<a name="2"></a>

## <center>自然主键和代理主键</center>


### 自然主键:

创建人员表时某条记录唯一确定，比如人都有身份证号，我们可以使用身份证号作为主键。  
(身份证号本身就是人员的一个属性，作为主键)
{: .notice}

### 代理主键:(多用)

创建人员表时某条记录唯一确定，但是没有使用身份证号作为主键，而是新建字段。
(用新建的字段作为主键，只是一个标识作用)
{: .notice}

***

<a name="3"></a>

## <center>主键生成策略</center>

|主键生成策略：|
|:--------|:-------|
|**increment**| 自动增长，适合short、int、long...类型 | 
|             |不是使用数据库的自动增长机制，而是Hibernate框架提供的自动增长方式|
|             |方式:先查询表的最大值select max(id) from 表;在最大值的基础上+1|
|             |问题:有多线程的问题，在集群下不要使用|
|:----        |:----     |
|**identity** |自动增长，适合short、int、long...类型|
|             |采用数据库的自动增长机制，但不适合于Oracle数据库(无自动增长)|
|:----        |:----     |
|**sequence** |序列，适用于short、int、long ...类型 |
|             |应用在Oracle上(mysql不支持序列)|
|:----        |:----     |
|**uuid**     |适用于字符串类型的主键，采用随机的字符串作为主键|
|:----        |:----     |
|**native**   |本地策略，根据底层数据库不同，自动选择适用identity还是sequence|
|:----        |:----     |
|**assigned** |Hibernate框架不维护主键，主键由程序自动生成(手动完成)|
|:----        |:----     |
|**foreign**  |主键的外来的(应用在多表一对一的关系)|
|:----        |:----     |
{: rules="groups"}
{: .notice}

***

<a name="4"></a>

## <center>持久化对象的三种状态</center>

| 状态 |特点|
|:--------:|:-------:|
| **Transient瞬时态** | 持久化对象没有唯一标识OID，没有纳入Session的管理 |
|:----|:----|  
| **Persistent持久态** | 持久化对象有唯一标识OID，已经纳入到Session的管理 |
|:----|:----|
| **Detached脱管态** | 持久化对象有唯一标识OID，没有纳入到Session管理 |
|:--------|:-------|
{: rules="groups"}
{: .notice}



### 区分三种持久化对象的状态

![](http://wx3.sinaimg.cn/large/83e1667dgy1fcjzp40szwj21hm0pygqd.jpg)

```java
@Test
// 区分持久化对象的三种状态:
public void demo(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();
	
	// 向数据库中保存一本图书:
	Book book = new Book();	// 1.瞬时态:没有唯一标识OID，没有与session关联
	book.setName("Hiernate开发");
	
	session.save(book); // 2.持久态:有唯一标识OID，与session关联
	
	// 3.事务提交
	tx.commit();
	// 4.释放资源
	session.close();
	
	book.setName("Struts2开发"); // 3.脱管态:有唯一的标识，session已经关闭，没有与session关联 
}
```


### 三种状态对象转换

* **瞬时态**

```
获得:
	Book book = new Book();

瞬时-->持久:
	* save(book);
	* save()/saveOrUpdate();

瞬时-->脱管:
	* book.setId(1);
```

* **持久态**

```
获得:
	Book book = (Book)session.get(Book.class,1);
	(1->唯一标识，session->session关联)
	* get()/load()/find()/iterate();

持久-->瞬时:
	* delete(book);
	* 特殊状态:删除态(被删除的对象，不建议去使用)

持久-->脱管:
	* session.close();
	* close()/clear()/evict(obj);
```

* **脱管态**

```
获得:
	Book book = new Book();
	book.setId(1);

脱管-->持久:
	* session.update();
	* update()/saveOrUpdate()/lock()

脱管-->瞬时:
	* book.setId(null);
```

![](http://wx2.sinaimg.cn/large/83e1667dgy1fcjzs3aujyj20ym0mogrh.jpg)


### 持久态对象有自动更新数据库的能力

```java
@Test
// 测试持久态的对象自动更新数据库
public void demo2(){
	// 1.创建Session
	Session session = HibernateUtils.openSession();
	// 2.开启事务
	Transaction tx = session.beginTransaction();
	
	// 获得一个持久态的对象.
	Book book = (Book) session.get(Book.class, 1);
	book.setName("Struts2开发");
	
	// session.update(book); <---不需要
	
	// 3.提交事务
	tx.commit();
	// 4.关闭资源
	session.close();
}
//自动更新数据库的能力依赖了Hibernate的一级缓存
```
