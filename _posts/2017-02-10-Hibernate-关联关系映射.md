- Hibernate---
layout: post
title:  "Hibernate-关联关系映射"
date:   2017-02-10
excerpt: "本篇介绍了Hibernate中一对多和多对多关系的配置"
tag:
- Hibernate
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">实体之间的关系</a>    
><a href="#2">一对多的配置</a>  
><a href="#3">Hibernate中的级联取值</a>  
><a href="#4">一对多中双向维护产生多余的SQL</a>   
><a href="#5">多对多的配置</a>   


***

<a name="1"></a>

## <center>实体之间的关系</center>  

**一对多：**

例：一个用户，生成多个订单，每一个订单只能属于一个用户。  
建表原则：在多的一方创建一个字段，作为外键，指向一的一方的主键。
{: .notice}

**多对多：**

例：一个学生可以选择多门课程，一个课程可以被多个学生选择。  
建表原则:创建第三张表，中间表至少有两个字段，分别作为外键指向多对多双方主键。
{: .notice}

**一对一：(很少用)**

例：一个公司只能有一个注册地址，一个注册地址，只能被一个公司使用(可将两个表建到一个表)  
建表原则：1.唯一外键：一对一的双方，假设一方是多的关系。需要在多的一方创建一个字段，作为外键，指向一的一方的主键，但是在外键添加一个unique。2.主键对应：一对一的双方，通过主键进行关联。
{: .notice}


***

<a name="2"></a>

## <center>一对多的配置</center>  

### 步骤

第一步:创建两个实体  
第二步:建立映射  
第三步:将映射放到核心配置文件中
{: .notice}

### 示例

```java
//客户实体:
public class Customer {
	private Integer cid;
	private String cname;	
	// 一个客户有多个订单(通常是set，不重复)
	private Set<Order> orders = new HashSet<Order>();
	
	public Integer getCid() {
		return cid;
	}
	public void setCid(Integer cid) {
		this.cid = cid;
	}
	public String getCname() {
		return cname;
	}
	public void setCname(String cname) {
		this.cname = cname;
	}
	public Set<Order> getOrders() {
		return orders;
	}
	public void setOrders(Set<Order> orders) {
		this.orders = orders;
	}	
}
```

```java
//订单实体:
public class Order {
	private Integer oid;
	private String addr;	
	// 订单属于某一个客户，放置一个客户的对象(面向对象)
	private Customer customer;

	public Integer getOid() {
		return oid;
	}
	public void setOid(Integer oid) {
		this.oid = oid;
	}
	public String getAddr() {
		return addr;
	}
	public void setAddr(String addr) {
		this.addr = addr;
	}
	public Customer getCustomer() {
		return customer;
	}
	public void setCustomer(Customer customer) {
		this.customer = customer;
	}
}
```

```xml
//Customer.hbm.xml
<hibernate-mapping>
	<class name="cn.xsw.hibernate.Customer" table="customer">
		<id name="cid" column="cid">
			<generator class="native"/>
		</id>	
		<property name="cname" column="cname" length="20"/>	
		
		<!-- 建立映射 -->
		<set name="orders"><!-- 多的一方属性名称，即private Set<Order> orders中的orders -->
			<key column="cno"></key><!-- 多的一方的外键的名称 -->
			<one-to-many class="cn.xsw.hibernate.Order"/><!-- 多的一方类的全路径 -->
		</set>
	</class>
</hibernate-mapping>
```

```xml
//Order.hbm.xml
<hibernate-mapping>
	<class name="cn.xsw.hibernate.Order" table="orders"><!-- 写order会出现错误，是关键字  -->
		<id name="oid" column="oid">
			<generator class="native"/>
		</id>
		<property name="addr" column="addr" length="50"/>
		
		<!-- 配置映射 -->
		<!-- 
		<many-to-one>标签
			name 	: 关联对象名称
			column	: 表中的外键名称
			class	: 关联对象类的全路径
		-->
		<many-to-one name="customer" column="cno" class="cn.xsw.hibernate.Customer"/>
	</class>
</hibernate-mapping>
```

映射配置总结：
在配置映射部分时，无论是一方还是多方，都按照：  
类中关联对象名-数据库表中外键名称-关联对象类全路径  
这样的方式来配置。其中要注意一和多的两方外键要相同。
{: .notice}


### 一对多中的级联保存

级联配置：`cascade=”save-update”`  
级联方向性：1.保存客户的时候，选择级联订单。2.保存订单的时候，选择级联客户。
{: .notice}

• **若不配置cascade，则需要双方都进行保存：**

```java
@Test
// 向客户表插入一个客户，在订单表中插入两个订单
public void demo1(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	// 定义一个客户:
	Customer customer = new Customer();
	customer.setCname("tom");
	
	// 定义两个订单:
	Order order1 = new Order();
	order1.setAddr("北京");
	
	Order order2 = new Order();
	order2.setAddr("上海");
	
	// 建立关系:
	order1.setCustomer(customer);
	order2.setCustomer(customer);
	
	customer.getOrders().add(order1);
	customer.getOrders().add(order2);
	
	// 保存:
	session.save(customer);
	session.save(order1);
	session.save(order2);
	
	tx.commit();
	session.close();
}
```

保存的时候只保存一方，且不配置casecade则会报异常:一个持久态对象关联一个瞬时的对象。
{: .notice}

• **保存客户级联订单**

```java
@Test
// <set>集合是客户的关联订单对象的集合
//所以在<set>上配置一个属性:cascade="save-update"
public void demo3(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	// 定义客户:
	Customer customer = new Customer();
	customer.setCname("tom");
	
	// 定义订单:
	Order order = new Order();
	order.setAddr("北京");
	order.setCustomer(customer);
	
	customer.getOrders().add(order);
	
	// 保存的时候只保存一方:
	session.save(customer);
	
	tx.commit();
	session.close();
}
```

• **保存订单级联客户**

```java
@Test
// 在Order.hbm.xml中<many-to-one>配置cascade属性:级联保存
public void demo4(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	// 定义客户:
	Customer customer = new Customer();
	customer.setCname("郭浩");
	
	// 定义订单:
	Order order = new Order();
	order.setAddr("西三旗中腾建华");
	order.setCustomer(customer);
	
	customer.getOrders().add(order);
	
	// 保存的时候只保存一方:
	session.save(order);
	
	tx.commit();
	session.close();
}
```

* **对象导航问题**  

![](http://wx4.sinaimg.cn/large/83e1667dgy1fcl917jk5sj212q0qyn1d.jpg)

### 一对多中的级联删除

级联配置：`cascade=”delete”`(与前面的save-update用","隔开)  

在JDBC情况下只删除一方会报错；  
在Hibernate中删除时默认的情况下不会报错，会删除数据记录，并将级联的一方外键置为null。

• 删除客户的时候级联删除订单

```java
// 在Customer.hbm.xml的<set>标签上配置cascade="delete"
Customer custoemr = (Customer) session.get(Customer.class, 1);
session.delete(custoemr);
//此时order中的cno置为null
```

• 删除订单的时候，级联删除客户

```java
Order order = (Order) session.get(Order.class, 1);
session.delete(order);
```

### 一对多中的孤儿删除

配置：`ascade="delete-orhpan"`		
仅限于一对多，因为只有一对多时候，才有父子存在。其中一的一方是父亲，多的一方是子方。  
当一个客户与某个订单解除了关系。将外键置为null。订单没有了所属客户，相当于一个孩子没有了父亲，就会将这种记录就删除。
{: .notice}

```java
//若不配置，默认情况下解除关系时把级联的订单外键设为null；配置则会把订单记录删除	
Customer customer = (Customer) session.get(Customer.class, 1);
Order order = (Order) session.get(Order.class, 1);
// 让1号客户与1号订单解除关系:
customer.getOrders().remove(order);
```

***

<a name="3"></a>

## <center>Hibernate中的级联取值</center>  


|  级联取值： |
|:--------|:-------|
| **none** | 不使用级联 |
|:----|:----|  
| **save-update** | 保存或更新的时候级联 |
|:----|:----|
| **delete** | 删除的时候级联 |
|:----|:----|
| **all** | 除了孤儿删除以外的所有级联 |
|:----|:----|
| **delete-orphan** | 孤儿删除(孤子删除) |
|:----|:----|
| **all-delete-orphan** | 包含了孤儿删除的所有的级联 |
|:--------|:-------|
{: rules="groups"}
{: .notice}

***

<a name="4"></a>

## <center>一对多中双向维护产生多余的SQL</center> 

在一对多中，双方都有外键的维护能力。当自动更新数据库时，会产生多余的SQL，即产生两次update语句。  
配置`inverse=”true”`:在哪一端配置，哪一端就放弃了外键的维护权。  
一般情况下，一的一方放弃外键的维护，由多的一方来维护。
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
| 1        | Shanghai  |   2     |
|:--------:|:-------:  |:-------:|
| 2        | Beijing   |   1     |
|:--------:|:-------:  |:-------:|
{: rules="groups"}
{: .notice}

现在想让oid为1的order不再与ann关联，而是与tom关联。  

```java
@Test
// 双向维护:
public void demo(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Customer customer = (Customer) session.get(Customer.class, 1);
	Order order = (Order) session.get(Order.class, 1);
	
	customer.getOrders().add(order);
	order.setCustomer(customer);
	
	tx.commit();
	session.close();
}
```

此时会看到自动更新数据库后，控制台上打印出两个update语句。出现这种情况的原因：  

![](http://wx4.sinaimg.cn/large/83e1667dgy1fcldv59tghj21o80hqafi.jpg)


### 区分cascade和inverse

casecade和inverse的区别:  
cascade:操作关联对象  
inverse:控制外键的维护
{: .notice}

```java
@Test
// 区分cascade和inverse
// 在Customer.hbm.xml中的<set>上配置 cascade="save-update" inverse="true"
	
Customer customer = new Customer();
customer.setCname("tom");

Order order = new Order();
order.setAddr("Beijing");

customer.getOrders().add(order);

session.save(customer);

// 客户是否存到数据库:存
// 订单是否存到数据库:存 因为cascade="save-update"；
// 但外键是null 因为inverse="true" 放弃维护外键的功能
```

***
***

<a name="5"></a>

## <center>多对多的配置</center>

第一步:创建实体类  
第二步:建立映射  
第三步:将映射文件加入到核心配置文件中
{: .notice}

```java
//学生的实体:
public class Student {
	private Integer sid;
	private String sname;
	// 一个学生选择多门课程:
	private Set<Course> courses = new HashSet<Course>();
	public Integer getSid() {
		return sid;
	}
	public void setSid(Integer sid) {
		this.sid = sid;
	}
	public String getSname() {
		return sname;
	}
	public void setSname(String sname) {
		this.sname = sname;
	}
	public Set<Course> getCourses() {
		return courses;
	}
	public void setCourses(Set<Course> courses) {
		this.courses = courses;
	}	
}
```

```java
//课程的实体:
public class Course {
	private Integer cid;
	private String cname;
	// 一个课程被多个学生选择:
	private Set<Student> students = new HashSet<Student>();
	
	public Integer getCid() {
		return cid;
	}
	public void setCid(Integer cid) {
		this.cid = cid;
	}
	public String getCname() {
		return cname;
	}
	public void setCname(String cname) {
		this.cname = cname;
	}
	public Set<Student> getStudents() {
		return students;
	}
	public void setStudents(Set<Student> students) {
		this.students = students;
	}
}
```

```xml
//Student.hbm.xml
<hibernate-mapping>
	<class name="cn.xsw.hibernate.Student" table="student">
		<id name="sid" column="sid">
			<generator class="native"/>
		</id>
		<property name="sname" column="sname" length="20"/>
		
		<!-- 配置关联映射 -->
		<set name="courses" table="stu_cour"><!--  name:学生中的课程集合的名称   table:中间表名称 -->
			<key column="sno"></key><!-- column:当前类在中间表的外键-->
			<many-to-many class="cn.xsw.hibernate.Course" column="cno"/>
			<!-- class:另一方类的全路径 column:另一方在中间表中外键名称-->
		</set>
	</class>
</hibernate-mapping>
```

```xml
//Course.hbm.xml
<hibernate-mapping>
	<class name="cn.xsw.hibernate3.Course" table="course">
		<id name="cid" column="cid">
			<generator class="native"/>
		</id>
		<property name="cname" column="cname" length="20"/>
		
		<!-- 配置关联映射 -->
		<set name="students" table="stu_cour"><!-- name:对应当前类中的学生的集合的名称  table:中间表的名称-->
			<key column="cno"></key><!-- column:当前类在中间表中外键 -->
			<many-to-many class="cn.xsw.hibernate.Student" column="sno"/>
			<!-- class:另一方的类全路径 column:另一方在中间表中外键名称 -->
		</set>
	</class>
</hibernate-mapping>
```


### 多对多中的保存操作

若不配置inverse则会报错，因为双向会产生多余的sql语句，即都在中间表中插入同一值的外键。  
一般在被动方的xml中配置inverse=“false”，由主动方来维护外键。
{: .notice}

```java
@Test
public void demo1() {
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();

	// 创建学生:
	Student student1 = new Student();
	student1.setSname("tom");
	Student student2 = new Student();
	student2.setSname("ann");

	// 创建课程:
	Course course1 = new Course();
	course1.setCname("Java语言");
	Course course2 = new Course();
	course2.setCname("Android语言");

	// 张三选1号和2号课
	student1.getCourses().add(course1);
	student1.getCourses().add(course2);

	course1.getStudents().add(student1);
	course2.getStudents().add(student1);

	student2.getCourses().add(course1);
	course1.getStudents().add(student2);

	// 执行保存:
	session.save(student1);
	session.save(student2);
	session.save(course1);
	session.save(course2);

	tx.commit();
	session.close();
}
```

### 多对多级中的级联保存

```java
@Test
// 级联保存:保存学生关联课程
// 在Student.hbm.xml中配置<set>上 cascade="save-update"
public void demo() {
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();

	// 创建学生:
	Student student1 = new Student();
	student1.setSname("tom");

	// 创建课程:
	Course course1 = new Course();
	course1.setCname("PHP语言");
	
	student1.getCourses().add(course1);
	course1.getStudents().add(student1);
	
	session.save(student1);

	tx.commit();
	session.close();
}
```

### 多对多级中的级联删除

级联删除在多对多中很少使用  

```java
@Test
// 删除:删除学生同时删除学生关联选课 cascade="delete"
public void demo3(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	Student student = (Student) session.get(Student.class, 3);
	session.delete(student);
	
	tx.commit();
	session.close();
}
```

适合的方式是退选remove：

```java
@Test
// 多对多的学生退选
public void demo4(){
	Session session = HibernateUtils.openSession();
	Transaction tx = session.beginTransaction();
	
	// 查询1号学生
	Student student = (Student) session.get(Student.class, 1);
	Course course = (Course) session.get(Course.class, 2);
	student.getCourses().remove(course);
	
	tx.commit();
	session.close();
}
```
