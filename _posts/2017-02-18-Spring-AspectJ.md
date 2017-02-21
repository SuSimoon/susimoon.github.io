---
layout: post
title:  "Spring-AspectJ"
date:   2017-02-18
excerpt: "本篇介绍了Spring中的AspectJ，以及基于注解和XML配置切面的方式"
tag:
- Java 
- Spring
- AOP
- AspectJ
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">AspectJ的概述</a>  
><a href="#2">基于注解配置切面</a>   
><a href="#3">AspectJ的通知类型</a>   
><a href="#4">基于XML配置切面</a>   
  
   

***

<a name="1"></a>

## <center>AspectJ的概述</center> 

AspectJ是一个基于Java语言的AOP框架。  
Spring2.0以后新增了对AspectJ切点表达式支持。  
@AspectJ是AspectJ1.5新增功能，通过JDK5注解技术，允许直接在Bean类中定义切面。  


### AspectJ所需jar包 

  
1.`spring-aop-3.2.0.RELEASE.jar`(aspectj依赖aop环境)  
2.`com.springsource.org.aopalliance-1.0.0.jar`(aspectj依赖aop环境)  
3.`spring-aspects-3.2.0.RELEASE.jar`  
4.`com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar`(依赖包中)
{: .notice}


***

<a name="2"></a>

## <center>基于注解配置切面</center> 

### AspectJ表达式：
  
`execution(表达式)`  
`execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)`(访问修饰符public等可省略)  

* 匹配指定包下所有类方法：`execution(* cn.xsw.dao.*(..))`  
* 匹配指定包及子包下所有类的方法：`execution(* cn.xsw.dao..*(..))`   
* 匹配指定类所有方法：`execution(* cn.xsw.service.UserService.*(..))`  
* 匹配当前类及其子类的所有方法：`execution(* cn.xsw.dao.UserDAO+.*(..))`  
* 匹配所有save开头的方法：`execution(* save*(..))`  
其中：* 任意返回类型 (..)表示参数任意 ..表示当前包和子包  + 继承、实现
{: .notice}

### 切点命名：

在每个通知内定义切点，会造成工作量大，不易维护。对于重复的切点，可以使用`@Poingcut`进行定义。  
切点方法：private void 无参数方法，方法名为切点名  
当通知多个切点时，可以使用|| 进行连接  


```java
//定义切点
@Pointcut("execution(* cn.xsw.spring.UserDao.find(..))")
	private void myPointcut(){}
```


### 程序示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 要引入aop约束 -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	
	<aop:aspectj-autoproxy/><!-- 开启AspectJ自动代理 -->
	<context:component-scan base-package="cn.xsw.spring"/>
</beans>
```

```java
public class UserDao {
	public void add(){
		System.out.println("添加用户");
	}
	public void update(){
		System.out.println("修改用户");
	}
}
```

```java
@Component
@Aspect //定义切面
public class MyAspect {
	//为哪些类添加增强
	@Before("execution(* cn.xsw.spring.UserDao.add(..))")
	public void before() {
		System.out.println("前置增强....");
	}
}
```


```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class SpringTest {
	@Autowired
	private UserDao userDao;
	@Test
	public void demo1() {
		userDao.add();
		userDao.update();
	}
}
```

result:  
前置增强....  
添加用户  
修改用户
{: .notice}

***

<a name="3"></a>

## <center>AspectJ的通知类型</center> 



|   AspectJ增强       | 
|:-------- |:------- |:------- |
| `@Before`  |前置通知 |没有办法阻止目标方法执行|
|:-------- |:------- |:------- |
| `@AfterReturning`  |后置通知|可获得方法返回值|
|:-------- |:------- |:------- |
| `@Around` | 环绕通知|可以阻止目标方法的执行|
|:-------- |:------- |:------- |
| `@AfterThrowing`| 抛出通知 |可获得异常信息|
|:-------- |:------- |:------- |
| `@After`   | 最终final通知|不管是否有异常，该通知都会执行|
|:-------- |:------- |:------- |
| `@DeclareParents`| 引介通知||
|:-------- |:------- |:------- |
{: rules="groups"}
{: .notice}


### 程序示例：

```java
@Component("userDao")
public class UserDao {
	public void add(){
		System.out.println("添加用户");
	}
	public String update(){
		System.out.println("修改用户");
		return "update";
	}
	public void find(){
		System.out.println("查询用户...");
	}
	public void delete(){
		System.out.println("删除用户...");
		int d =1/0;
	}
}
```

```java
@Component
@Aspect 
public class MyAspect {
	//为切点命名
	@Pointcut("execution(* cn.xsw.spring.UserDao.add(..))")
	private void addPoint(){}
	@Pointcut("execution(* cn.xsw.spring.UserDao.update(..))")
	private void updatePoint(){}

	@Before("MyAspect.addPoint()")
	public void before(JoinPoint joinPoint) { //joinPoint显示应用到那个方法上
		System.out.println("前置增强...."+joinPoint);
	}
	
	@AfterReturning(value="updatePoint()",returning="returnVal") 
	public void afterReturin(Object returnVal){
		System.out.println("后置增强....方法的返回值:"+returnVal);
	}
	
	@Around("execution(* cn.xsw.spring.UserDao.find(..))")
	public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
		System.out.println("环绕前增强....");
		Object obj = proceedingJoinPoint.proceed();
		System.out.println("环绕后增强....");
		return obj;
	}
	@AfterThrowing(value="execution(* cn.xsw.spring.UserDao.delete(..))",throwing="e")
	public void afterThrowing(Throwable e){
		System.out.println("异常通知..."+e.getMessage());
	}
	@After("execution(* cn.xsw.spring.UserDao.delete(..))")
	public void after(){
		System.out.println("最终通知...");
	}	
}
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class SpringTest {
	@Autowired
	private UserDao userDao;
	@Test
	public void demo1() {
		userDao.add();
		userDao.update();
		userDao.find();
		userDao.delete();
	}
}
```

result:  
前置增强....execution(void cn.xsw.spring.UserDao.add())  
添加用户  
------------  
修改用户  
后置增强....方法的返回值:update  
------------  
环绕前增强....  
查询用户...  
环绕后增强....  
------------  
删除用户...  
最终通知...  
异常通知.../ by zero
{: notice}

***

<a name="4"></a>

## <center>基于XML配置切面</center> 

1.前置通知:

```xml
代码:
public void before(){
	System.out.println("前置通知...");
}

配置:
<aop:config>
	<!-- 定义切点: -->
	<aop:pointcut expression="execution(* cn.xsw.spring.ProductDao.add(..))" id="mypointcut"/>
	<aop:aspect ref="myAspectXML">
		<!-- 前置通知 -->
		<aop:before method="before" pointcut-ref="mypointcut"/>
	</aop:aspect>
</aop:config>
```

2.后置通知:

```xml
代码:
public void afterReturing(Object returnVal){
	System.out.println("后置通知...返回值:"+returnVal);
}

配置:
<aop:config>
		<!-- 后置通知 -->
		<aop:after-returning method="afterReturing" pointcut-ref="mypointcut" returning="returnVal"/>
	</aop:aspect>
</aop:config>
```
3.环绕通知:

```xml
代码:
public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
	System.out.println("环绕前增强....");
	Object result = proceedingJoinPoint.proceed();
	System.out.println("环绕后增强....");
	return result;
}

配置:
<aop:config>
	<!-- 定义切面: -->
	<aop:aspect ref="myAspectXML">
		<!-- 环绕通知 -->
		<aop:around method="around" pointcut-ref="mypointcut"/>
	</aop:aspect>
</aop:config>
```

4.异常通知:

```xml
代码:
public void afterThrowing(Throwable e){
	System.out.println("异常通知..."+e.getMessage());
}

配置:
<aop:config>
		<!-- 异常通知 -->
		<aop:after-throwing method="afterThrowing" pointcut-ref="mypointcut" throwing="e"/>
	</aop:aspect>
</aop:config>
```

5.最终通知:

```xml
代码:
public void after(){
	System.out.println("最终通知....");
}

配置:
<aop:config>
		<!-- 最终通知 -->
		<aop:after method="after" pointcut-ref="mypointcut"/>
	</aop:aspect>
</aop:config>
```

***

注：Advisor和Aspect的区别：  

Advisor:Spring传统意义上的切面，支持一个切点和一个通知的组合。
Aspect:可以支持多个切点和多个通知的组合。  
{: .notice}

