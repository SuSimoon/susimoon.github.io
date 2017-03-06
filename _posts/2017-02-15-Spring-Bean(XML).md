---
layout: post
title:  "Spring-Bean(XML)"
date:   2017-02-15
excerpt: "本篇介绍了Spring中基于XML配置方式的Bean，包括Bean实例化的方式、属性配置、属性注入以及Bean的生命周期"
tag:
- Spring
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">Bean实例化的方式</a>  
><a href="#2">Bean的属性配置</a>   
><a href="#3">Bean的属性注入</a>  
><a href="#4">Bean的生命周期</a>   
   

***

<a name="1"></a>

## <center>Bean实例化的方式</center>  

Spring框架提供了三种方式实例化Bean的方式：  
	
* 无参构造方法实例化  
* 静态工厂实例化(了解)  
* 实例工厂实例化(了解)
{: .notice}

### 程序示例

**• 无参构造方法实例化：** 

```xml
<!-- 默认情况下使用的就是无参数的构造方法 -->
<bean id="bean1" class="cn.xsw.spring.Bean1"></bean>
```

```java
public class Bean1 {
	
}
```

```java
@Test
public void demo1() {
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext(
			"applicationContext.xml");
	Bean1 bean1 = (Bean1) applicationContext.getBean("bean1");
	System.out.println(bean1);
}
```


***

<a name="2"></a>

## <center>Bean的属性配置</center>  

### id和name的区别

• id：遵守XML约束中的id的约束，id约束保证这个属性的值是唯一的。  
而且必须以字母开始，可以使用字母、数字、连字符、下划线、句话、冒号。    
• name：没有这些要求，如果bean标签上没有配置id，那么name可以作为id使用。
{: .notice}

注：现在的开发中都使用id属性即可。
{: .notice}

### Bean的作用范围(scope)


**scope属性：**  

singleton: 单例的(默认值)  
prototype: 多例的  
request: web开发中，创建了一个对象，将这个对象存入request范围，request.setAttribute();  
session: web开发中，创建了一个对象，将这个对象存入session范围，session.setAttribute();  
globalSession: 一般用于Porlet应用环境，指的是分布式开发。不是porlet环境，globalSession等同于session;
{: .notice}

注：实际开发中主要使用singleton，prototype。
{: .notice}

**程序示例：**

* 当不写scope属性或scope="singleton"时，Customer类只被实例化一次，控制台打印一次"Customer类被实例化"，两个对象地址相同。  
* 当scope="prototype"时，每次创建新的对象，Customer类被实例化两次，控制台打印两次"Customer类被实例化"，两个对象地址不同。

```xml
<bean id="customer" class="cn.xsw.spring.Customer" scope="prototype"/>
```

```java
public class Customer {
	public Customer() {
		System.out.println("Customer类被实例化");
	}	
}
```

```java
@Test
// 测试Scope
public void demo1() {
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext(
			"applicationContext.xml");
	Customer c1 = (Customer) applicationContext.getBean("customer");
	Customer c2 = (Customer) applicationContext.getBean("customer");
	System.out.println(c1==c2);
	//默认单例，c1==c2
	//scope="prototype" c1、c2不等
}
```

***

<a name="3"></a>

## <center>Bean的属性注入</center> 

### 注入方式


1.构造器注入  
2.setter方法注入
{: .notice}


**1.构造器注入**


使用`<constructor-arg/>`  

```xml
<bean id="car" class="cn.xsw.spring.Car">
	<constructor-arg name="name" value="宝马"/>
	<constructor-arg name="price" value="1000000"/> 
	
	<!-- 或使用下标索引(type可不写) -->
	<constructor-arg index="0" type="java.lang.String" value="奔驰"/>
	<constructor-arg index="1" type="java.lang.Double" value="2000000"/>
</bean>
```

```java
public class Car {
	private String name;
	private Double price;
	
	public Car() {
	}

	public Car(String name, Double price) {
		this.name = name;
		this.price = price;
	}

	@Override
	public String toString() {
		return "Car [name=" + name + ", price=" + price + "]";
	}	
}
```


**2.setter方法注入(常用)**

使用`<property/>`    
注入普通属性用`value`  
注入对象用`ref`  


```xml
<bean id="car2" class="cn.xsw.spring.Car2">
	<!-- value:普通属性的值，ref:引用其他的对象 -->
	<property name="name" value="保时捷"/>
	<property name="user" value="tom"/>
</bean>
```

```java
public class Car2 {
	private String name;
	private User user;
	
	public void setName(String name) {
		this.name = name;
	}
	public void setUser(User user) {
		this.user = user;
	}
}
```



### 名称空间

若类中的属性过多，在xml文件中注入会比较麻烦。  
Spring2.5版本引入了名称空间p，可以用来简化属性的注入。  

1.引入名称空间:  
`xmlns:p="http://www.springframework.org/schema/p"`

2.在bean中配置：

p:<属性名>="xxx" 引入常量值  
p:<属性名>-ref="xxx" 引用其它Bean对象
{: .notice}

```xml
<bean id="car" class="..." p:name="宝马" p:price="400000"/>
<bean id="user" class="..." p:name="tom" p:car-ref="car"/>
```

### pEL注入方式


Spring3.0版本提供的注入属性方式。

语法：`#{表达式}`  
`<bean id="" value="#{表达式}">`  


```xml
<bean id="car" class="cn.xsw.spring.Car">
	<property name="name" value="#{'大众'}"/>
	<property name="user" value="#{user}"/>
</bean>
```


### 集合属性的注入

```java
public class CollectionBean {
	private List<String> list;
	private Set<String> set;
	private Map<String,Integer> map;
	private Properties properties;
	
	public void setSet(Set<String> set) {
		this.set = set;
	}

	public void setList(List<String> list) {
		this.list = list;
	}

	public void setMap(Map<String, Integer> map) {
		this.map = map;
	}

	public void setProperties(Properties properties) {
		this.properties = properties;
	}
}
```

```xml
<bean id="collectionBean" class="cn.xsw.spring.CollectionBean">
	<!-- 注入List集合 -->
	<property name="list">
		<list>
			<value>a</value>
			<value>b</value>
		</list>
	</property>
	
	<!-- 注入set集合 -->
	<property name="set">
		<set>
			<value>a</value>
			<value>b</value>
		</set>
	</property>
	
	<!-- 注入map集合 -->
	<property name="map">
		<map>
			<entry key="a" value="111"/>
			<entry key="b" value="333"/>
		</map>
	</property>
	
	<property name="properties">
		<props>
			<prop key="username">root</prop>
			<prop key="password">123</prop>
		</props>
	</property>
</bean>
```


***

<a name="4"></a>

## <center>Bean的生命周期</center> 

Bean的生命周期的11个步骤:(5、8重要)

1.instantiate bean 对象实例化  
2.populate properties 封装属性  
3.如果Bean实现BeanNameAware执行setBeanName  
4.如果Bean实现BeanFactoryAware 或者ApplicationContextAware设置工厂 setBeanFactory 或者上下文对象 setApplicationContext  
5.如果存在类实现BeanPostProcessor(后处理Bean)，执行postProcessBeforeInitialization  
6.如果Bean实现InitializingBean 执行 afterPropertiesSet   
7.调用<bean init-method="init"> 指定初始化方法 init  
8.如果存在类实现BeanPostProcessor，执行postProcessAfterInitialization  
9.执行业务处理  
10.如果Bean实现DisposableBean执行destroy  
11.调用<bean destroy-method="customerDestroy"> 指定销毁方法 customerDestroy
{: .notice}

### 程序示例1


配置Bean的初始化和销毁的方法:

在bean标签设置`init-method=””`和`destroy-method=””`。  
执行销毁的时候，必须手动关闭工厂，而且只对scope=”singleton”有效。
{: .notice}

```xml
<bean id="product" class="cn.xsw.spring.Product" init-method="setup" destroy-method="teardown">
	<property name="name" value="TV"></property>
</bean>
```

```java
public class Product {
	private String name;
	
	public void setName(String name) {
		this.name = name;
	}

	public void setup(){
		System.out.println("初始化方法执行...");
	}
	
	public void teardown(){
		System.out.println("销毁的方法执行...");
	}

	@Override
	public String toString() {
		return "Product [name=" + name + "]";
	}
	
}
```

```java
@Test
// 测试初始化和销毁的方法
public void demo(){
	ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext(
			"applicationContext.xml");
	Product p1 = (Product) applicationContext.getBean("product");
	System.out.println(p1);
	applicationContext.close(); //子类才有关闭方法，所以要用ClassPathXmlApplicationContext
}
```

result：  

初始化方法执行...  
Product [name=TV]  
销毁的方法执行...
{: .notice}


### 程序示例2


若想要在CustomerService类的add方法之前进行权限校验，可以使用BeanPostProcessor(后处理Bean)。


```xml
<bean id="customerService" class="cn.xsw.spring.CustomerServiceImpl">
	</bean>
<!-- 不用写id，spring自动识别 -->
<bean class="cn.xsw.spring.MyBeanPostProcessor"></bean>
```

```java
//CustomerService.java
public interface CustomerService {
	public void add();
	public void find();
}
```

```java
//CustomerServiceImpl.java
public class CustomerServiceImpl implements CustomerService {
	@Override
	public void add() {
		System.out.println("添加客户...");
	}
	@Override
	public void find() {
		System.out.println("查询客户...");
	}
}
```

```java
//MyBeanPostProcessor.java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor implements BeanPostProcessor{
	/**
	 * bean:实例对象
	 * beanName:在配置文件中配置的类的标识
	 */
	public Object postProcessBeforeInitialization(Object bean, String beanName)
			throws BeansException {
		return bean;
	}

	public Object postProcessAfterInitialization(final Object bean, String beanName)
			throws BeansException {
		System.out.println("第八步:初始化后执行...");
		// 动态代理:对某些类(customerService)增强
		if(beanName.equals("customerService")){
			Object proxy = Proxy.newProxyInstance(bean.getClass().getClassLoader(), bean.getClass().getInterfaces() , new InvocationHandler() {
				// 调用目标方法的时候,调用invoke方法.
				public Object invoke(Object proxy, Method method, Object[] args)
						throws Throwable {
					// 针对add方法进行权限验证
					if("add".equals(method.getName())){
						System.out.println("权限校验...");
						Object result = method.invoke(bean, args);
						//System.out.println(System.currentTimeMillis());
						return result;
					}
					return method.invoke(bean, args);
				}
			});
			return proxy;
		}
		return bean;
	}
}
```

```java
@Test
public void demo() {
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext(
			"applicationContext.xml");
	
	CustomerService customerService = (CustomerService) applicationContext.getBean("customerService");
	customerService.add();
	customerService.find();

}
```

