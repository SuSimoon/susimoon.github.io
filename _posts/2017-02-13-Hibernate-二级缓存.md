- Hibernate---
layout: post
title:  "Hibernate-二级缓存"
date:   2017-02-12
excerpt: "本篇介绍了Hibernate中的二级缓存相关内容"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  


***


## <center>Hibernate中的二级缓存</center> 

<a name="1"></a>

### <center>概念</center>

![](http://wx4.sinaimg.cn/large/83e1667dgy1fcoum5f98rj21bi0noq4n.jpg)

Hibernate中提供了两个级别的缓存:  

• 第一级别的缓存是Session级别的缓存，它是属于事务范围的缓存。这一级别的缓存由hibernate管理的，一般情况下无需进行干预。  
• 第二级别的缓存是SessionFactory级别的缓存，它是属于进程范围的缓存。
{: .notice}


Hibernate的缓存可以分为两类:  

• 内置缓存: Hibernate自带的，不可卸载。  
通常在Hibernate的初始化阶段，Hibernate会把映射元数据和预定义的SQL语句放到SessionFactory的缓存中，映射元数据是映射文件中数据的复制，而预定义SQL语句时Hibernate根据映射元数据推导出来的。该内置缓存是只读的。  
• 外置缓存(二级缓存): 一个可配置的缓存插件。在默认情况下，SessionFactory不会启用这个缓存插件。   外置缓存中的数据是数据库数据的复制，外置缓存的物理介质可以是内存或硬盘。
{: .notice}


***
<a name="2"></a>

### <center>二级缓存的结构</center>

类缓存区、集合缓存区、更新时间戳、查询缓冲区  

![](http://ww4.sinaimg.cn/large/83e1667djw1f9mqm0km07j21c00pewlo.jpg)

***
<a name="3"></a>

### <center>二级缓存中的数据特点</center>


适合放入二级缓存中的数据:  

很少被修改   
不是很重要的数据，允许出现偶尔的并发问题
{: .notice}

不适合放入二级缓存中的数据:  

经常被修改  
财务数据，绝对不允许出现并发问题  
与其他应用数据共享的数据
{: .notice}

***
<a name="4"></a>

### <center>二级缓存的配置(配置EHCache缓存)</center>

**1.导入jar包：**

拷贝ehcache-1.5.0.jar到当前工程的lib目录下:  
• backport-util-concurrent  
• commons-logging
{: .notice}

**2.在主配置文件中配置：** 

• 开启二级缓存:  
`<property name="hibernate.cache.use_second_level_cache">true</property>`  
• 要指定缓存的供应商:  
`<property name="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</property>`
{: .notice}

**3.指定使用二级缓存的类:**

• 方法一:在使用类的*.hbm.xml配置

选择需要使用二级缓存的持久化类，设置它的二级缓存的并发访问策略。  
`<class>`元素的cache子元素表明Hibernate会缓存对象的简单属性，但不会缓存集合属性。  
若希望缓存集合属性中的元素，必须在`<set>`元素中加入`<cache>`子元素。    

```xml	
<class name="cn.xsw.Customer" talbe="customer" lazy="false">
	<!-- 配置类级别的二级缓存 -->
	<cache usage="read-write"/>

<set name="orders" talbe="orders" fetch="selsect" lazy="false">
	<!-- 配置集合级别的二级缓存 -->
	<cache usage="read-write"/>
```


• 方法二:在hibernate.cfg.xml文件中配置(建议)

```xml
<!-- 指定使用二级缓存的类 放在maping下面 -->
<!-- 配置类级别的二级缓存 -->
<class-cache class="cn.xsw.Customer" usage="read-write"/>
<class-cache class="cn.xsw.Order" usage="read-write"/>
<!-- 配置集合级别的二级缓存 类路径.集合名-->
<collection-cache  collection="cn.xsw.Customer.orders" usage="read-write"/>
```


**5.配置EHCache默认的配置文件ehcache.xml（放在类下）**

***

<a name="5"></a>

### <center>程序示例</center>  

当没有配置二级缓存时，两个不同的session查询相同的数据时会发送两条SQL。  
当配置二级缓存后，则只发一条SQL。     

```java
public void demo() {
	// 未配置二级缓存	
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Customer customer1 = (Customer) session.get(Customer.class, 1); //发送SQL
	
	tx.commit();
	session.close();

	session = HibernateUtils.openSession();
	tx = session.beginTransaction();
	
	Customer customer2 = (Customer) session.get(Customer.class, 1); //发送SQL
	
	System.out.println(customer1 == customer3);
	
	tx.commit();
	session.close();
}
```

***
<a name="6"></a>

### <center>二级缓存中类缓存区的特点</center>


类缓存区存储的是散装的数据(cid对其它属性有引用，当需要对象时会把数据重装成对象)  
第一个是true(相同的地址)，第二个是false(不同的对象)。
{: .notice}

{% capture images %}
	http://wx1.sinaimg.cn/large/83e1667dgy1fcp1d19vbyj21qi0m844d.jpg
{% endcapture %}
{% include gallery images=images cols=1 %}


***

<a name="7"></a>

### <center>集合缓存区的特点</center>


缓存的是对象的id，通过id去查询，所以需要依赖类缓冲区的配置。  
如果在配置中没有配置订单的类，即没有`<class-cache class="cn.xsw.Order" usage="read-write"/>`  
则在查询订单时会发送多条搜索订单的SQL语句。所以也需要设置订单类。
{: .notice}

{% capture images %}
	http://wx3.sinaimg.cn/large/83e1667dgy1fcp280y79aj21p40hu0xs.jpg
{% endcapture %}
{% include gallery images=images cols=1 %}


***

<a name="8"></a>

### <center>查询缓存</center>

查询缓存比二级缓存功能更加强大，但需要依赖于二级缓存。  

测试类级别的二级缓存只适用于get和load获取数据。  
对query接口可以将查询的数据放置到二级缓存中，但不能使用list方法从缓存中获取数据。  
若配置了查询缓存，则可以使用缓存中的数据。   
{: .notice}

二级缓存与查询缓存的区别：  

二级缓存:只能是对类/对象的缓存。  
查询缓存:针对类中属性的缓存。
{: .notice}


配置：`<property name="cache.use_query_cache">true</property>`  
设定：`query.setCacheble(true);`
{: .notice}



```java
@Test
// 查询缓存的测试
public void demo9(){
	Session session = HibernateUtils.getCurrentSession();
	Transaction tx = session.beginTransaction();
	
	Query query = session.createQuery("select c.cname from Customer c");
	// 使用查询缓存:
	query.setCacheable(true);
	query.list();
	
	tx.commit();
	
	session = HibernateUtils.getCurrentSession();
	tx = session.beginTransaction();
	
	query = session.createQuery("select c.cname from Customer c");
	query.setCacheable(true);
	query.list();
	
	tx.commit();
}
```

***

<a name="9"></a>

### <center>iterate()方法</center>

iterate()方法也同list()方法一样可以查询所有信息。  
不同的是，iterate方法会发送N+1条SQL查询语句，先查询id，再查询N个对象。  
当开启二级缓存时，iterate会使用二级缓存的数据，而query则需要配置才能使用。  

```java
public void demo(){
	Session session = HibernateUtils.getCurrentSession();
	Transaction tx = session.beginTransaction();
	
	Iterator<Customer> iterator = session.createQuery("from Customer").iterate();
	while(iterator.hasNext()){
		Customer customer = iterator.next();
		System.out.println(customer);
	}
	
	tx.commit();
	
	session = HibernateUtils.getCurrentSession();
	tx = session.beginTransaction();
	
	iterator = session.createQuery("from Customer").iterate(); //不发SQL
	while(iterator.hasNext()){
		Customer customer = iterator.next();
		System.out.println(customer);
	}
	
	tx.commit();
}
```

***

<a name="10"></a>

### <center>一级缓存更新数据会同步到二级缓存</center>

```java
public void demo6(){
	Session session = HibernateUtils.getCurrentSession();
	Transaction tx = session.beginTransaction();
	
	Customer customer = (Customer) session.get(Customer.class, 1);
	customer.setCname("ann"); // 修改名字
	
	tx.commit();
	
	session = HibernateUtils.getCurrentSession();
	tx = session.beginTransaction();
	
	Customer customer2 = (Customer) session.get(Customer.class, 1);
	System.out.println(customer2.getCname()); // 获得更新后的名字ann
	
	tx.commit();
}
```

***

<a name="11"></a>

### <center>更新时间戳区域</center>

{% capture images %}
	http://ww1.sinaimg.cn/large/83e1667djw1f9msjwts2gj21te0tin4f.jpg
{% endcapture %}
{% include gallery images=images cols=1 %}
