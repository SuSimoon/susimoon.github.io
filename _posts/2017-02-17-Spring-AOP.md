---
layout: post
title:  "Spring-AOP"
date:   2017-02-17
excerpt: "本篇介绍了Spring中AOP的概述以及其底层的实现"
tag:
- Java 
- Spring
- AOP
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">AOP的概述</a>  
><a href="#2">AOP的底层实现</a>   
   

***

<a name="1"></a>

## <center>AOP的概述</center> 


### AOP的概念

AOP: Aspect Oriented Programing 面向切面编程  

AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码。  
AOP使用纯Java实现，不需要专门的编译过程和类加载器，在运行期通过代理方式向目标类织入增强代码。  


### AOP底层原理

就是代理机制，包含两种技术：

**1.JDK动态代理:**对实现了接口的类生成代理(没实现接口则不能生成代理对象)  
**2.CGLib代理机制:**对类生成代理
{: .notice}


### AOP的术语

|    术语   |   概念  | 
|:-------- |:------- |
| Joinpoint(连接点)|指那些被拦截到的方法 |
|:-------- |:------- |
| Pointcut(切入点)|指我们要对哪些Joinpoint进行拦截的定义|
|:-------- |:------- |
| Advice(通知/增强) | 拦截到Joinpoint之后所要做的事情 |
|:-------- |:------- |
| Introduction(引介)| 在不修改类代码的前提下，可以在运行期为类动态地添加一些方法或Field |
|:-------- |:------- |
| Target(目标对象)| 代理的目标对象 |
|:-------- |:------- |
| Weaving(织入)| 把增强应用到目标对象来创建新的代理对象的过程|
|:-------- |:------- |
| Proxy(代理)  | 一个类被AOP织入增强后，就产生一个结果代理类 |
|:-------- |:------- |
| Aspect(切面)| 是切入点和通知的结合|
|:-------- |:------- |
{: rules="groups"}
{: .notice}


***

<a name="2"></a>

## <center>AOP的底层实现</center> 


### JDK动态代理

```java
public interface UserDao {
	public void add();
	public void update();
}
```

```java
public class UserDaoImpl implements UserDao {
	public void add() {
		System.out.println("添加用户...");
	}

	public void update() {
		System.out.println("修改用户...");
	}
}
```

```java
public class JDKProxy implements InvocationHandler {
	private UserDao userDao;

	public JDKProxy(UserDao userDao) {
		super();
		this.userDao = userDao;
	}

	public UserDao createProxy() {
		UserDao proxy = (UserDao) Proxy.newProxyInstance(userDao.getClass()
				.getClassLoader(), userDao.getClass().getInterfaces(), this);
		return proxy;
	}

	// 调用目标对象的任何一个方法，都相当于调用invoke();
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		if("add".equals(method.getName())){
			// 记录日志:
			System.out.println("日志记录=================");
			Object result = method.invoke(userDao, args);
			return result;
		}
		return method.invoke(userDao, args);
	}
}
```


```java
@Test
public void demo(){
	// 被代理对象
	UserDao userDao = new UserDaoImpl();
	// 创建代理对象的时候传入被代理对象
	UserDao proxy = new JDKProxy(userDao).createProxy();
	proxy.add();
	proxy.update();
}
```

### CGLIB动态代理


CGLIB(Code Generation Library)是一个开源项目。  
是一个强大的，高性能，高质量的Code生成类库，它可以在运行期扩展Java类与实现Java接口。   
CGLIB生成代理机制:生成了一个真实对象的子类。  
现在做cglib的开发，可以不用直接引入cglib的包，已经在spring的核心中已集成cglib。  


```java
public class ProductDao {
	public void add(){
		System.out.println("添加商品...");
	}
	
	public void update(){
		System.out.println("修改商品...");
	}
}
```

```java
public class CGLibProxy implements MethodInterceptor{
	//被代理对象
	private ProductDao productDao;
    //构造方法传入被代理对象
	public CGLibProxy(ProductDao productDao) {
		super();
		this.productDao = productDao;
	}
	
	public ProductDao createProxy(){
		// 使用CGLIB生成代理:
		// 1.创建核心类:
		Enhancer enhancer = new Enhancer();
		// 2.为其设置父类:机制是继承的方式
		enhancer.setSuperclass(productDao.getClass());
		// 3.设置回调:
		enhancer.setCallback(this);
		// 4.创建代理:
		return (ProductDao) enhancer.create();
	}

	public Object intercept(Object proxy, Method method, Object[] args,
			MethodProxy methodProxy) throws Throwable {
		if("add".equals(method.getName())){
			System.out.println("日志记录==============");
			Object obj = methodProxy.invokeSuper(proxy, args); //要调用子类代理对象
			return obj;
		}
		return methodProxy.invokeSuper(proxy, args);
	}
}
```

```java
@Test
public void demo(){
	ProductDao productDao = new ProductDao();
	ProductDao proxy = new CGLibProxy(productDao).createProxy();
	proxy.add();
	proxy.update();
}
```


**结论:**  
在Spring框架中，如果类实现了接口，就使用JDK的动态代理生成代理对象；  
如果这个类没有实现任何接口，使用CGLIB生成代理对象。
{: .notice}