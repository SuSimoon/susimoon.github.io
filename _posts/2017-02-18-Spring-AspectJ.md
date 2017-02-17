---
layout: post
title:  "Spring-AspectJ"
date:   2017-02-18
excerpt: "本篇介绍了Spring"
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
><a href="#2">注解配置切面</a>   
><a href="#3">XML配置切面</a>   
  
   

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

## <center>注解配置切面</center> 

### AspectJ表达式:
  
`execution(表达式)`  
`execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)`(访问修饰符public等可省略)  

* 匹配指定包下所有类方法：`execution(* cn.xsw.dao.*(..))`  
* 匹配指定包及子包下所有类的方法：`execution(* cn.xsw.dao..*(..))`   
* 匹配指定类所有方法：`execution(* cn.xsw.service.UserService.*(..))`  
* 匹配当前类及其子类的所有方法：`execution(* cn.xsw.dao.UserDAO+.*(..))`  
* 匹配所有save开头的方法：`execution(* save*(..))`  
其中：* 任意返回类型 (..)表示参数任意 ..表示当前包和子包  + 继承、实现
{: .notice}


### AspectJ增强:

|   注解    | 作用     | 
|:-------- |:------- |
| `@Before`  |前置通知   |
|:-------- |:------- |
| `@AfterReturning`|后置通知|
|:-------- |:------- |
| `@Around`  | 环绕通知|
|:-------- |:------- |
| `@AfterThrowing`| 抛出通知 |
|:-------- |:------- |
| `@After`   | 最终final通知|
|:-------- |:------- |
| `@DeclareParents`| 引介通知|
|:-------- |:------- |
{: rules="groups"}
{: .notice}

### 程序示例


```java
package cn.itcast.spring3.demo1;

public class UserDao {
	public void add(){
		System.out.println("添加用户");
	}
	public int update(){
		System.out.println("修改用户");
		return 1;
	}
	public void delete(){
		System.out.println("删除用户");
	}
	public void find(){
		System.out.println("查询用户");
		//int d = 1/ 0;
	}
}
```

```java
第三步:使用AspectJ注解形式:

@Aspect //用来定义切面
public class MyAspect {
	//为哪些类添加增强
	@Before("execution(* cn.itcast.spring3.demo1.UserDao.add(..))")
	public void before(){
		System.out.println("前置增强....");
	}
}
```


```
第四步:创建applicationContext.xml
	* 引入aop的约束:
	* <aop:aspectj-autoproxy /> --- 开启aspectj自动代理

		<aop:aspectj-autoproxy />
		<bean id="userDao" class="cn.itcast.spring3.demo1.UserDao"></bean>
		<bean id="myAspect" class="cn.itcast.spring3.demo1.MyAspect"></bean>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" <-----引入aop的schema
       xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	<!-- 自动生成代理  底层就是AnnotationAwareAspectJAutoProxyCreator -->
	<aop:aspectj-autoproxy />

	<bean id="userDao" class="cn.itcast.spring3.demo1.UserDao"></bean>

	<bean id="myAspect" class="cn.itcast.spring3.demo1.MyAspect"></bean>
</beans>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringTest1 {

	@Autowired
	@Qualifier("userDao")
	private UserDao userDao;
	
	@Test
	public void demo1(){
		userDao.add();
		userDao.delete();
		userDao.update();
		userDao.find();
	}
}
```

```
AspectJ的通知类型:
	@Before 
		前置通知，相当于BeforeAdvice
		就在方法之前执行，没有办法阻止目标方法执行的。
	
	@AfterReturning 
		后置通知，相当于AfterReturningAdvice
		后置通知，获得方法返回值。
	
	@Around 
		环绕通知，相当于MethodInterceptor
		在可以方法之前和之后来执行的，而且可以阻止目标方法的执行。
	
	@AfterThrowing
		抛出通知，相当于ThrowAdvice。
	
	@After 
		最终final通知，不管是否异常，该通知都会执行
	
	@DeclareParents 
		引介通知，相当于IntroductionInterceptor(不要求掌握)
```

```
切点的定义:
	@Pointcut("execution(* cn.itcast.spring3.demo1.UserDao.find(..))")
		private void myPointcut(){}

面试:
	Advisor和Aspect的区别?
		* Advisor:Spring传统意义上的切面:支持一个切点和一个通知的组合
		* Aspect:可以支持多个切点和多个通知的组合
```

```java
public class MyAspect {
	
	@Before("execution(* cn.itcast.spring3.demo1.UserDao.add(..))")
	public void before(JoinPoint joinPoint){ //joinPoint显示应用到那个方法上
		System.out.println("前置增强...."+joinPoint); 
	}
	
	@AfterReturning(value="execution(* cn.itcast.spring3.demo1.UserDao.update(..))",returning="returnVal") //returnVal用来记录方法返回值
	public void afterReturin(Object returnVal){
		System.out.println("后置增强....方法的返回值:"+returnVal);
	}
	
	@Around(value="MyAspect.myPointcut()")
	public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
		System.out.println("环绕前增强....");
		Object obj = proceedingJoinPoint.proceed();
		System.out.println("环绕后增强....");
		return obj;
	}
	
	@AfterThrowing(value="MyAspect.myPointcut()",throwing="e")
	public void afterThrowing(Throwable e){
		System.out.println("出异常了..."+e.getMessage());
	}
	
	@After("MyAspect.myPointcut()")
	public void after(){
		System.out.println("最终通知...");
	}
	
	@Pointcut("execution(* cn.itcast.spring3.demo1.UserDao.find(..))")
	private void myPointcut(){}
}
```


* 基于XML

```
第一步:编写被增强的类:
	ProductDao

第二步:定义切面
```

```java
package cn.itcast.spring3.demo2;

public class ProductDao {
	public int add(){
		System.out.println("添加商品...");
		int d = 10/0;
		return 100;
	}
	public void update(){
		System.out.println("修改商品...");
	}
	public void delete(){
		System.out.println("删除商品...");
	}
	public void find(){
		System.out.println("查询商品...");
	}
}
```

```java
package cn.itcast.spring3.demo2;

import org.aspectj.lang.ProceedingJoinPoint;

/**
 * 切面类
 * @author 姜涛
 *
 */
public class MyAspectXML {
	
	public void before(){
		System.out.println("前置通知...");
	}
	
	public void afterReturing(Object returnVal){
		System.out.println("后置通知...返回值:"+returnVal);
	}
	
	public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
		System.out.println("环绕前增强....");
		Object result = proceedingJoinPoint.proceed();
		System.out.println("环绕后增强....");
		return result;
	}
	
	public void afterThrowing(Throwable e){
		System.out.println("异常通知..."+e.getMessage());
	}
	
	public void after(){
		System.out.println("最终通知....");
	}
}
```

```xml
第三步:配置applicationContext.xmll

1.前置通知:
	* 代码:
		public void before(){
			System.out.println("前置通知...");
		}

	* 配置:
		<aop:config>
			<!-- 定义切点: -->
			<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
			<aop:aspect ref="myAspectXML">
				<!-- 前置通知 -->
				<aop:before method="before" pointcut-ref="mypointcut"/>
			</aop:aspect>
		</aop:config>

2.后置通知:
	* 代码:
		public void afterReturing(Object returnVal){
			System.out.println("后置通知...返回值:"+returnVal);
		}

	* 配置:
		<aop:config>
			<!-- 定义切点: -->
			<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
			<aop:aspect ref="myAspectXML">
				<!-- 后置通知 -->
				<aop:after-returning method="afterReturing" pointcut-ref="mypointcut" returning="returnVal"/>
			</aop:aspect>
		</aop:config>

3.环绕通知:
	* 代码:
		public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
			System.out.println("环绕前增强....");
			Object result = proceedingJoinPoint.proceed();
			System.out.println("环绕后增强....");
			return result;
		}

	* 配置:
		<aop:config>
			<!-- 定义切点: -->
			<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
			<!-- 定义切面: -->
			<aop:aspect ref="myAspectXML">
				<!-- 前置通知 -->
				<!-- <aop:before method="before" pointcut-ref="mypointcut"/> -->
				<!-- 后置通知 -->
				<!-- <aop:after-returning method="afterReturing" pointcut-ref="mypointcut" returning="returnVal"/> -->
				<!-- 环绕通知 -->
				<aop:around method="around" pointcut-ref="mypointcut"/>
			</aop:aspect>
		</aop:config>

4.异常通知:
	* 代码;
		public void afterThrowing(Throwable e){
			System.out.println("异常通知..."+e.getMessage());
		}

	* 配置;
		<aop:config>
			<!-- 定义切点: -->
			<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
			<aop:aspect ref="myAspectXML">
				<!-- 异常通知 -->
				<aop:after-throwing method="afterThrowing" pointcut-ref="mypointcut" throwing="e"/>
			</aop:aspect>
		</aop:config>

5.最终通知:
	* 代码:
		public void after(){
			System.out.println("最终通知....");
		}

	* 配置:
		<aop:config>
			<!-- 定义切点: -->
			<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
			<aop:aspect ref="myAspectXML">
				<!-- 最终通知 -->
				<aop:after method="after" pointcut-ref="mypointcut"/>
			</aop:aspect>
		</aop:config>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	<!-- 定义被增强的类 -->
	<bean id="productDao" class="cn.itcast.spring3.demo2.ProductDao"></bean>
	
	<!-- 定义切面 -->
	<bean id="myAspectXML" class="cn.itcast.spring3.demo2.MyAspectXML"></bean>
	
	<!-- 定义aop配置 -->
	<aop:config>
		<!-- 定义切点: -->
		<aop:pointcut expression="execution(* cn.itcast.spring3.demo2.ProductDao.add(..))" id="mypointcut"/>
		<aop:aspect ref="myAspectXML">
			<!-- 前置通知 -->
			<!-- <aop:before method="before" pointcut-ref="mypointcut"/> -->
			<!-- 后置通知 -->
			<!-- <aop:after-returning method="afterReturing" pointcut-ref="mypointcut" returning="returnVal"/> -->
			<!-- 环绕通知 -->
			<!-- <aop:around method="around" pointcut-ref="mypointcut"/> -->
			<!-- 异常通知 -->
			<!-- <aop:after-throwing method="afterThrowing" pointcut-ref="mypointcut" throwing="e"/> -->
			<!-- 最终通知 -->
			<aop:after method="after" pointcut-ref="mypointcut"/>
		</aop:aspect>
	</aop:config>
</beans>
```

```java
package cn.itcast.spring3.demo2;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext2.xml")
public class SpringTest2 {

	@Autowired
	@Qualifier("productDao")
	private ProductDao productDao;
	
	@Test
	public void demo1(){
		productDao.add();
		productDao.find();
		productDao.update();
		productDao.delete();
	}
}
```
