---
layout: post
title:  "Spring-Bean(Annotation)"
date:   2017-02-16
excerpt: "本篇介绍了Spring中基于XML配置方式的Bean，包括Bean实例化的方式、属性配置、属性注入以及Bean的生命周期"
tag:
- Java 
- Spring
- Bean
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">Spring的注解装配Bean</a>  
><a href="#2">Bean的属性注入</a>   
><a href="#3">Bean其它的属性配置</a>  
><a href="#4">Bean的生命周期</a>   
   

***

<a name="1"></a>

## <center>Spring的注解装配Bean</center> 

Spring2.5引入使用注解去定义Bean。

**步骤：**  
1.要在applicationContext.xml中引入the context schema  
2.配置自动扫描：`<context:component-scan base-package="..."/>`  
3.xml和注解混合方式配置时要加：`<context:annotation-config/>`  
4.用`@Component`描述Spring框架中Bean
{: .notice}

与@Component注解等效的三个注解: (为了后续版本进行增强，但目前作用相同) 

`@Controller` 用于对Controller实现类进行标注  
`@Service` 用于对Service实现类进行标注  
`@Repository` 用于对DAO实现类进行标注
{: .notice}


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
	
	<context:component-scan base-package="cn.xsw.spring"/>

</beans>
```

```java
@Component("userService") //等同于提供Bean的id
public class UserService {
	public void sayHello(){
		System.out.println("Hello Spring Annotation");
	}
}
```

```java
@Test
public void demo() {
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext(
			"applicationContext.xml");	
	UserService userService = (UserService) applicationContext.getBean("userService");
	userService.sayHello();
}
```

***

<a name="2"></a>

## <center>Bean的属性注入</center> 

属性注入时不需要提供set方法。  

### 1.普通属性

`@Value(value="")`

**程序示例：**

```java
@Component("userService")
public class UserService {
	@Value(value="spring")
	private String info;
	
	public void sayHello() {
		System.out.println("hello" + info);
	}
}
```


### 2.对象属性

两种方式：   

1.按类型注入：`@Autowired` 默认按照类型进行注入。  
2.按名称注入：`@Autowired`+`@Qualifier("user")`
需要在User类中提供Bean的名称。    
等价于 `@Resource(name="user")`
{: .notice}

**程序示例：按类型注入**

```java
//按类型注入：
@Component("userService") 
public class UserService {
	@Autowired 
	private UserDao userDao;
}
```

```java
@Repository
public class UserDao {
	
}
```

**程序示例：按名称注入**

```java
//按名称注入：
@Component("userService")
public class UserService {
	@Autowired
	@Qualifier("userDao") //<----
	private UserDao userDao;
}
```

```java
@Repository("userDao") //<----
public class UserDao {
	
}
```

***


<a name="3"></a>

## <center>Bean其它的属性配置</center> 


### 配置Bean初始化方法和销毁方法:

`@PostConstruct` 初始化 xml中的init-method配置   
`@PreDestroy`    销毁 xml中的destroy-method配置
{: .notice}

```java
@PostConstruct
public void setup() {
	...
}

@PreDestroy
public void destroy() {
	...
}
```


### 配置Bean的作用范围:

在类上加`@Scope(value="")`

***

<a name="2"></a>

## <center>Bean的属性注入</center>

* Spring3.0提供使用Java类定义Bean信息的方法

```java
//类结构特别复杂时可以使用这种方式

@Configuration //代表这个类是配置类
public class BeanConfig {

	@Bean(name="car")
	public Car showCar(){
		Car car = new Car();
		car.setName("长安");
		car.setPrice(40000d);
		return car;
	}
	
	@Bean(name="product")
	public Product initProduct(){
		Product product = new Product();
		product.setName("空调");
		product.setPrice(3000d);
		return product;
	}
}
```

```java
package cn.itcast.spring3.demo2;

import org.junit.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SprintTest {
	
	@Test
	public void demo1(){
		ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext(
				"applicationContext.xml");
		
		Car car = (Car) applicationContext.getBean("car");
		Product product = (Product) applicationContext.getBean("product");
		System.out.println(car);
		System.out.println(product);
	}
}
```

```java
package cn.itcast.spring3.demo2;

public class Car {
	private String name;
	private Double price;
	public void setName(String name) {
		this.name = name;
	}
	public void setPrice(Double price) {
		this.price = price;
	}
	@Override
	public String toString() {
		return "Car [name=" + name + ", price=" + price + "]";
	}
}
```

```java
package cn.itcast.spring3.demo2;

public class Product {
	private String name;
	private Double price;
	public void setName(String name) {
		this.name = name;
	}
	public void setPrice(Double price) {
		this.price = price;
	}
	@Override
	public String toString() {
		return "Product [name=" + name + ", price=" + price + "]";
	}	
}
```

* 实际开发中使用XML还是注解

```
* XML:
	bean管理(bean的注册)

* 注解:
	注入属性的时候比较方便(不用提供set方法)

* 两种方式结合:
	一般使用XML注册Bean，使用注解进行属性的注入。

配置文件中要有<context:annotation-config/> -- 注解生效
```
