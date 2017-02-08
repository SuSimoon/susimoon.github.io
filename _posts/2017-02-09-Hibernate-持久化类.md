---
layout: post
title:  "Hibernate-持久化类"
date:   2017-02-07
excerpt: "本篇介绍了Hibernate中持久化类的编写规则"
tag:
- Java 
- Hibernate
- 持久化类
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">持久化类编写规则</a>    
><a href="#2">自然主键和代理主键</a>  
><a href="#3">主键生成策略</a>  

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

