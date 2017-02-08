---
layout: post
title:  "Hibernate-CRUD"
date:   2017-02-07
excerpt: "本篇介绍了Hibernate的增删改查操作"
tag:
- Java 
- Hibernate
- CRUD
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

><a href="#1">C：保存数据</a>    
><a href="#2">R：查询数据(单个对象)</a>  
><a href="#3">R：查询数据(查询所有)</a>  
><a href="#4">U：修改数据</a>    
><a href="#5">D：删除数据</a>


***

<a name="1"></a>

## <center>保存数据</center>

* `session.save(customer);`  

***

<a name="2"></a>

## <center>查询数据(单个对象)</center>

### 根据主键进行查询

* `Customer customer = (Customer)session.get(Customer.class ,1);`
* `Customer customer = (Customer)session.load(Customer.class,1);`


### get和load的区别

**1.发送SQL的时机:**
	
* load方法采用了lazy延迟加载(懒加载)技术，真正使用这个对象的数据的时候才会加载。  
其中“使用这个对象的数据”不包括主键，例如：  
System.out.println(customer.getId()); 不会加载。  
使用age、name等对象属性时会加载。
* get方法是立即检索。当执行session.get()方法的时候，马上发送SQL语句查询。
{: .notice}

**2.返回的对象:**

* load方法返回的是代理对象。  
* get方法返回的是真实的对象。
{: .notice}

**3.查询一个不存在的数据:**  

* load方法抛异常:ObjectNotFoundException
* get方法抛异常:NullPointException
{: .notice}

```java
@Test
// 按id进行查询
public void demo(){
	// get方法进行查询
	Customer customer = (Customer) session.get(Customer.class, 1); // 马上发生一条SQL进行查询
	System.out.println(customer);
	
	// load方法进行查询
	Customer customer = (Customer) session.load(Customer.class, 1); // 没有发送SQL
	System.out.println(customer); // 发送SQL
}
```

注：get和load方法返回的是Object类型，需要强转。
{: .notice}  

***

<a name="3"></a>

## <center>查询数据(查询所有)</center>

### HQL

HQL:Hibernate Query Language

```java
// 1.查询所有的客户
Query query = session.createQuery("from Customer");
//Customer指的是类，映射文件中已经有了所以不需要写带包名的类名
List<Customer> list = query.list();

// 2.按名称查询
Query query = session.createQuery("from Customer where name = ?");
query.setParameter(0, "tom");
//或:
Query query = session.createQuery("from Customer where name = :aaa");
query.setParameter("aaa", "tom");
List<Customer> list = query.list();

for (Customer customer : list) {
	System.out.println(customer);
}
```


### QBC

Query By Criteria(条件查询)  

```java
// 查询所有:QBC
Criteria criteria = session.createCriteria(Customer.class);
List<Customer> list = criteria.list();

// 按名称查询
Criteria criteria = session.createCriteria(Customer.class);
criteria.add(Restrictions.eq("name", "tom"));
List<Customer> list = criteria.list();

for (Customer customer : list) {
	System.out.println(customer);
}
```


### SQL

```java
// 查询所有:SQL
SQLQuery query = session.createSQLQuery("select * from customer");
List<Object[]> list = query.list(); // 没有告知对象所以返回的是Object数组

for (Object[] objs : list) {
	System.out.println(Arrays.toString(objs));
}

SQLQuery query = session.createSQLQuery("select * from customer");
query.addEntity(Customer.class); 
List<Customer> list = query.list(); // 此时已告知对象，返回的是对象的数组
for (Customer customer : list) {
	System.out.println(customer);
}
```

注：具体讲解看API章节。
{: .notice}

***

<a name="4"></a>

## <center>修改数据</center>

* `session.update(customer);`

### 修改的两种方式

**1.手动创建对象方式**

创建一个新的对象，设置这个对象的主键，修改对象的属性内容。  
不推荐，因为当没有设置某个属性(age)时，就会将这个属性的默认值存入，比如int类型的age默认成了0。
{: .notice}

**2.先查询再修改方式(推荐方式)**   

先查询对象，在对其属性进行修改。
{: .notice}

```java
@Test
// 修改记录
public void demo(){
	// 1.手动创建对象的方式
	Customer customer = new Customer();
	customer.setId(2);
	customer.setName("tom");
	
	session.update(customer);
	
	// 2.先查询再修改的方式
	Customer customer = (Customer) session.get(Customer.class, 1);
	customer.setName("tom");
	
	session.update(customer);
}
```

***

<a name="5"></a>

## <center>删除数据</center>


* `session.delete(customer);`

删除记录的两种方式:  

1.手动创建对象的方式  
2.先查询再删除的方式(可以级联删除-删掉用户同时删掉其订单)
{: .notice}

```java
// 1.手动创建对象的方式
	Customer customer = new Customer();
	customer.setId(2);
	session.delete(customer);

// 2.先查询再删除的方式
	Customer customer = (Customer)session.get(Customer.class, 1);
	session.delete(customer);
```
