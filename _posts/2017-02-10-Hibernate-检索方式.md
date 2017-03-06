---
layout: post
title:  "Hibernate-检索方式"
date:   2017-02-10
excerpt: "本篇介绍了Hibernate中的检索方式与检索策略"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">Hibernate的检索方式</a>    
><a href="#2">HQL和QBC检索</a>  
><a href="#3">多表连接查询</a>  
><a href="#4">Hibernate的检索(抓取)策略</a>  
><a href="#5">set集合上的检索策略(在一方对多方配置)</a>   
><a href="#6">many-to-one上的检索策略(在多方对一方配置)</a>   
><a href="#7">批量抓取 </a> 


***

<a name="1"></a>

## <center>Hibernate的检索方式</center> 

	
**1.导航对象图检索方式:**   

根据已经加载的对象导航到其他对象  
Customer customer = (Customer)session.get(Customer.class,1);  
customer.getOrders();
{: .notice}

**2.OID检索方式:**   

按照对象的OID来检索对象  
get()/load();方法进行检索
{: .notice}


**3.HQL检索方式:**   

使用面向对象的HQL(Hibernate Query Language)查询语言  
面向对象的查询/支持方法链编程  
Query query = session.createQuery(“HQL”);
{: .notice}


**4.QBC检索方式:**  

使用QBC(Query By Criteria)API来检索对象  
这种API封装了基于字符串形式的查询语句，提供了更加面向对象的查询接口。  
Criteria criteria = session.createCriteria(Customer.class);
{: .notice}


**5.本地SQL检索方式:**     

使用本地数据库的SQL查询语句  
SQLQuery query = session.createSQLQuery(“SQL”);
{: .notice}

***

<a name="2"></a>

## <center>HQL和QBC检索</center> 

HQL:语句是面向对象型的，但符合数据库格式
QBC:语句是面向对象型的，符合面向对象的操作

### 1.查询所有记录:

```java
HQL
	List<Customer> list = session.createQuery("from Customer").list();
	for (Customer customer : list) {
		System.out.println(customer);
	}

QBC	
	List<Customer> list = session.createCriteria(Customer.class).list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
SQL
	List<Object[]> list = session.createSQLQuery("select * from customer").list();
	for (Object[] objects : list) {
		System.out.println(Arrays.toString(objects));
	}

    // 也可以装入实体中
	List<Customer> list = session.createSQLQuery("select * from customer")
			.addEntity(Customer.class).list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
```

### 2.查询使用别名:

```java
HQL
	// 使用别名(别名as可以省略)
	List<Customer> list = session.createQuery("from Customer  c").list();
	System.out.println(list);
	 
	// 使用别名:带参数
	List<Customer> list = session
			.createQuery("from Customer as c where c.cname = ?")
			.setString(0, "tom").list();
	System.out.println(list);
	
	// 不支持 select * from Customer写法
	// 但可以写成 select 别名 from Customer as 别名;
	List<Customer> list = session.createQuery("select c from Customer c").list();
	System.out.println(list);
```

### 3.排序:

```java
HQL
	List<Customer> list = session.createQuery(
			"from Customer c order by c.id desc").list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
QBC
	List<Customer> list = session.createCriteria(Customer.class)
			.addOrder(org.hibernate.criterion.Order.desc("id")).list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
```

### 4.分页查询:

```java
HQL
	Query query = session.createQuery("from Order");
	query.setFirstResult(20);
	query.setMaxResults(10);
	List<Order> list = query.list();
	for (Order order : list) {
		System.out.println(order);
	}
QBC
	Criteria criteria = session.createCriteria(Order.class);
	criteria.setFirstResult(10);
	criteria.setMaxResults(10);
	List<Order> list = criteria.list();
	for (Order order : list) {
		System.out.println(order);
	}
```

### 5.单个对象查询:

```java
//list多个，uniqueResult一个
//若无该对象则返回null
HQL
	Customer customer = (Customer) session
			.createQuery("from Customer where cname = ?")
			.setString(0, "小明").uniqueResult(); 
	System.out.println(customer);
QBC
	Customer customer = (Customer) session.createCriteria(Customer.class)
			.add(Restrictions.eq("cname", "小明")).uniqueResult();
	System.out.println(customer);	
```

### 6.参数绑定:

```java
HQL
	// 1.使用?号方式绑定
	Query query = session.createQuery("from Customer where cname = ? and cid =?");
	query.setString(0, "tom");
	query.setInteger(1,3);
	List<Customer> list = query.list();
	System.out.println(list);
	
	// 2.使用名称的方式绑定
	Query query = session.createQuery("from Customer where cname=:name and cid=:id");
	query.setString("name", "tom"); //query.setParameter("name", "tom");
	query.setInteger("id", 3); //query.setParameter("id", 3);
	List<Customer> list = query.list();
	System.out.println(list);

	// 3.绑定实体(当参数是实体时)
	Customer cusomer = new Customer();
	customer.setCid(1); // 只需设置id

	List<Order> list = session
			.createQuery("from Order o where o.customer = ?") 
			.setEntity(0, customer).list(); // 不能写setParameter
	for (Order order : list) {
		System.out.println(order);
	}
QBC
	//一个条件
	List<Customer> list = session.createCriteria(Customer.class)
	 .add(Restrictions.eq("cname", "小明")).list();
	System.out.println(list);
	
	//多个条件
	List<Customer> list = session.createCriteria(Customer.class)
			.add(Restrictions.eq("cname", "小明"))
			.add(Restrictions.eq("cid", 2)).list();
	System.out.println(list);
```

### 7.投影操作:查询对象的某几个属性

```java
HQL
	// 查询客户的名称:	
	 List<Object> list = session.createQuery(
	 "select c.cname from Customer c").list();
	 System.out.println(list);
	
	// 查询客户的名称和id:	
	 List<Object[]> list = session.createQuery(
	 "select c.cid,c.cname from Customer c").list(); 
	 for (Object[] objects: list) { 
	 	System.out.println(Arrays.toString(objects)); 
	 }
	 
	// 把属性装入对象中:
	List<Customer> list = session.createQuery(
			"select new Customer(cname) from Customer").list();
			// 要在Customer类中加相应的构造
	System.out.println(list); // 会打印出对象
QBC
	List list = session.createCriteria(Customer.class)
		.setProjection(Projections.projectionList()
		.add(Property.forName("name"))
		.add(Property.forName("city"))).list();

```

### 8.模糊查询:

```java
HQL
	Query query = session.createQuery("from Customer where cname like ?");
	query.setParameter(0, "小%");
	List<Customer> list = query.list();
	System.out.println(list); 
QBC
	Criteria criteria = session.createCriteria(Customer.class);
	criteria.add(Restrictions.like("cname", "大%"));
	List<Customer> list = criteria.list();
	System.out.println(list);
```

### 9.使用聚集函数:

```java
HQL
	Query query = session.createQuery("select count(*) from Customer c");
	Long count=(Long)query.uniqueResult();
```

### 10.命名查询:

```xml
//Customer.hbm.xml
<query name="findAll">
	from Customer
</query>
```

```java
HQL
	List<Customer> list = session.getNamedQuery("findAll").list();
	System.out.println(list);
```

### 11.离线条件查询:

有些数据事没办法事先封装好的，比如在页面上需要上传的内容。  
我们知道，Criteria对象需要在dao层由session来创建，而在web层，DetachedCriteria可以实现离线封装。  
可以将这类数据在web层先由DetachedCriteria封装好，传到dao层再去执行。
{: .notice}

![](http://ww2.sinaimg.cn/large/83e1667djw1f9f9veeyw5j216o0qoqbp.jpg)

```java
@Test
public void demo(){
	// web层的封装
	DetachedCriteria criteria = DetachedCriteria.forClass(Customer.class);
	criteria.add(Restrictions.eq("cname", "小明"));
	criteria.add(Restrictions.eq("cid", 2));
	
	// 传递到DAO层
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Criteria c1 = criteria.getExecutableCriteria(session);
	List<Customer> list = c1.list();
	System.out.println(list);

	tx.commit();
	session.close();
}
```

***

<a name="3"></a>

## <center>多表连接查询</center>


SQL多表查询:  


1.连接:交叉连接: `select * from A,B;`  
2.内连接:查询的是两个表的交集    
`select * from A inner join B on A.字段 = B.字段;`  
`select * from A,B where A.字段 = B.字段;(隐式内连接)`  
3.外连接:  
左外连接:`select * from A left outer join B on  A.字段 = B.字段;`  
右外连接:`select * from A right outer join B on A.字段 = B.字段;`
{: .notice}	

HQL多表的查询:
	
交叉连接  
内连接  
隐式内连接  
迫切内连接 <---  
左外连接  
迫切左外连接 <---  
右外连接
{: .notice}	

HQL的内连接和迫切内连接区别:  

内连接查询:将数据封装一个List<Object[]>中  
迫切内连接:将数据封装一个List<Customer>中，但会得到重复记录，需要使用distinct。
{: .notice}	


### 示例  

假设数据库中两张表的内容如下：  
**customer:**  

|  cid     | cname   |
|:--------:|:-------:|
| 1        |   tom   |
|:---- :   |:----:   |  
| 2        |   ann   |
|:-------- |:------- |
{: rules="groups"}
{: .notice}

**order:**  

|  oid     | addr      | cno    |
|:--------:|:-------:  |:-------:|
| 1        | Shanghai  |   1     |
|:--------:|:-------:  |:-------:|
| 2        | Beijing   |   1     |
|:--------:|:-------:  |:-------:|
| 3        | Guangzhou |   2     |
|:--------:|:-------:  |:-------:|
{: rules="groups"}
{: .notice}


```java
@Test
public void demo(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	// 内连接查询的是两个表的交集部分:
	Query query = session.createQuery("from Customer c inner join c.orders");
	List<Object[]> list = query.list();
	for (Object[] objects : list) {
		System.out.println(Arrays.toString(objects));
	}
	
	// 迫切内连接:使用一个关键字 fetch(HQL)
	Query query = session.createQuery("select distinct c from Customer c  inner join fetch c.orders");
	List<Customer> list = query.list();
	for (Customer customer : list) {
		System.out.println(customer);
	}
	
	tx.commit();
	session.close();
}
```

结果:

```java
/*
 * Object形式封装:
 */
1.Query query = session.createQuery("from Customer c inner join c.orders");

Customer [cid=1, cname=tom, orders=[Order [oid=1, addr=Shanghai], Order [oid=2, addr=Beijing]]]
Customer [cid=1, cname=tom, orders=[Order [oid=1, addr=Shanghai], Order [oid=2, addr=Beijing]]]
Customer [cid=2, cname=ann, orders=[Order [oid=3, addr=Guangzou]]]

/*
 * 对象形式封装，但重复了:
 */
2.Query query = session.createQuery("from Customer c  inner join fetch c.orders");

Customer [cid=1, cname=tom, orders=[Order [oid=1, addr=Shanghai], Order [oid=2, addr=Beijing]]]
Customer [cid=1, cname=tom, orders=[Order [oid=1, addr=Shanghai], Order [oid=2, addr=Beijing]]]
Customer [cid=2, cname=ann, orders=[Order [oid=3, addr=Guangzou]]]

/*
 * 加distinct:
 */
3.Query query = session.createQuery("select distinct c from Customer c  inner join fetch c.orders");

Customer [cid=1, cname=tom, orders=[Order [oid=2, addr=Beijing], Order [oid=1, addr=Shanghai]]]
Customer [cid=2, cname=ann, orders=[Order [oid=3, addr=Guangzou]]]
;
```


***
***

<a name="4"></a>

## <center>Hibernate的检索(抓取)策略</center>

### 立即检索和延迟检索


• 立即检索:  

`get();`  
当执行某行代码的时候，马上发出SQL语句进行查询。
{: .notice}

• 延迟检索:

`load();`   
当执行某行代码的时候，不会马上发出SQL语句进行查询，当真正使用这个对象的时候才会发送SQL语句。   
延迟检索失效: 1.持久化类设置为final。 2.映射文件`<class>`元素中属性`lazy="false"`。
{: .notice}


### 类级别检索和关联级别检索

• 类级别的检索:

通过`class`元素的lazy属性设定。  
session的方法直接检索Customer对象，对Customer对象到底采用立即检索还是延迟检索方式。
{: .notice}

• 关联级别的检索:

通过`set`元素lazy属性设定。      
查询客体表是否发生，以何种方式发生(立即检索、延迟检索和迫切左外连接检索)，就是关联级别检索。
{: .notice}


注：类级别就是在映射文件上面的class中进行配置，而关联级别就是在下面的关联对象里配置。
{: .notice}

***
<a name="5"></a>

## <center>set集合上的检索策略(在一方对多方配置)</center>

|**fetch** |  控制sql语句的类型              |
|:--------:|:-------:|
| join     |  发送迫切左外连接的SQL查询关联对象 |
|:----:    |:----:   |  
| select   |  默认值，发送多条SQL查询关联对象   |
|:--------: |:-------: |
| subselect|  发送子查询查询关联对象           |
|:--------: |:-------: |
{: rules="groups"}
{: .notice}


|**lazy**  |  控制关联对象的检索是否采用延迟       |
|:--------:|:-------:|
| true     |  默认值，查询关联对象的时候使用延迟检索 |
|:----:    |:----:   |  
| false    |  查询关联对象的时候不使用延迟检索      |
|:--------: |:-------: |
| extra    |  极其懒惰 |
|:--------: |:-------: |
{: rules="groups"}
{: .notice}


**1.fetch="select"，lazy="true":**  

该配置是默认情况  

```java
/*
 *  <set>没有配置fetch和lazy情况
 */
Customer customer = (Customer) session.get(Customer.class, 1);// 发送查询客户的SQL(但没有查询订单的SQL)
System.out.println(customer.getOrders().size());// 又发送一条SQL，去查询客户的关联的订单
```


**2.fetch="select"，lazy="false":**   

```xml
//Customer.hbm.xml
<set name="orders" cascade="save-update" fetch="join" lazy="false">
	<key column="cno"/>
	<one-to-many class="cn.xsw.test.Order"/>
</set>
```

```java
/*
 * 关联对象检索不使用延迟
 */
Customer customer = (Customer) session.get(Customer.class, 1); //发送查询客户和订单的两条SQL语句
System.out.println(customer.getOrders().size());
```


**3.在`<set>`集合上配置fetch="join":**   

`fetch="join"`则lazy属性被忽略。   
在`<set>`集合上配置fetch="join"后，运行到该语句时会发送左外连接的SQL语句。  


```xml
//Customer.hbm.xml
<set name="orders" cascade="save-update" fetch="join">
	<key column="cno"/>
	<one-to-many class="cn.xsw.test.Order"/>
</set>
```

```java
/*
 * 发送迫切做外连接查询两个表
 */
Customer customer = (Customer) session.get(Customer.class, 1); //直接发送一条迫切左外连接
System.out.println(customer.getOrders().size());
```


**4.fetch="select"，lazy="extra":** 

运行到查询订单数量时，这种设置只发送查询数量的语句，而不发送查询订单的语句。  
即select count(oid) from orders where cno...  

```xml
//Customer.hbm.xml
<set name="orders" cascade="save-update" fetch="select" lazy="extra">
	<key column="cno"/>
	<one-to-many class="cn.xsw.test.Order"/>
</set>
```

```java
/*
 * 发送迫切做外连接查询两个表
 */
Customer customer = (Customer) session.get(Customer.class, 1);  // 发送查询客户的SQL
System.out.println(customer.getOrders().size());  // 只发送查询数量的SQL
```

**5.fetch="subselect"，lazy="true":** 

使用subselect的时候，需要使用query接口进行测试。  
因为子查询适用于查询多个用户，例如：select * from orders where cno in (1,2,3);  
只查询一个用户时用=即可，例如：select * from orders where cno = 1;  

```xml
//Customer.hbm.xml
<set name="orders" cascade="save-update" fetch="subselect" lazy="true">
	<key column="cno"/>
	<one-to-many class="cn.xsw.test.Order"/>
</set>
```

```java
List<Customer> list = session.createQuery("from Customer").list();	
for(Customer customer : list) {
	System.out.println(customer.getOrders().size());
}
```

***
<a name="6"></a>

## <center>many-to-one上的检索策略(在多方对一方配置)</center>


|**fetch** |  控制sql语句的类型       |
|:--------:|:-------:|
| join     |  发送迫切左外连接的SQL查询关联对象 |
|:---- :   |:----:   |  
| select   |  发送多条SQL检索关联对象 |
|:---- :   |:----:   | 
{: rules="groups"}
{: .notice}

|**lazy**  |  控制关联对象的检索是否采用延迟    |
|:--------:|:-------:|  
| false    |  查询关联对象的时候不使用延迟检索  |
|:-------- |:------- |
| proxy    |  使用代理 |
|:---- :   |:----:   |
| no-proxy |  不使用代理 |
|:-------- |:------- |
{: rules="groups"}
{: .notice}

**1.不配置的情况:** 

```java
/*
 * 没有在<many-to-one>标签上配置:
 * 	* 发送多条SQL进行查询
 */
Order order = (Order) session.load(Order.class, 1); // 只发送一条查询订单的SQL
System.out.println(order.getCustomer().getCname()); // 使用客户时，又发送一条SQL查询订单关联的客户
```

**2.fetch="select"，lazy="false":** 


```xml
<many-to-one name="customer" column="cno" class="..." fetch="select" lazy="false"/>		
```

```java
Order order = (Order) session.load(Order.class, 1); // 发送条查询订单和客户的两条SQL
System.out.println(order.getCustomer().getCname());
```


**3.fetch="select"，lazy="proxy":** 

检索订单额时候，是否马上检索客户，由Customer对象的映射文件中<class>上lazy的属性来决定。  

***

<a name="7"></a>

## <center>批量抓取</center> 

批量抓取可以减少与数据库交互次数。  

• 通过客户抓取订单：在客户一端配置，`<set>`集合上配置batch-size="3"。  
• 通过订单批量抓取客户：在订单的`<many-to-one>`上没有batch-size属性，因此需要在客户一端的`<class>`标签上配置batch-size()。  

```java
Transaction tx = session.beginTransaction();

List<Customer> list = session.createQuery("from Customer").list();
for (Customer customer : list) {
	for (Order order : customer.getOrders()) {
		System.out.println(order.getAddr());
	}
}
```

```java
List<Order> list = session.createQuery("from Order").list();
for (Order order : list) {
	System.out.println(order.getCustomer().getCname());
}
}
```