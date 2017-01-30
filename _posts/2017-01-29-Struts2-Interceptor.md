---
layout: post
title:  "Struts2-Interceptor"
date:   2017-01-30
excerpt: "本篇介绍了Struts2中拦截器的概念、如何自定义拦截器、拦截器的原理以及它与过滤器的区别"
tag:
- Java 
- Struts2
- Interceptor
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

>拦截器的介绍
>自定义拦截器
>拦截器原理
>Interceptor与Filter区别


***


## <center>拦截器的介绍</center>


1.Struts2拦截器使用的是AOP思想。  
2.AOP的底层实现就是动态代理。  
3.拦截器采用责任链模式:   
* 在责任链模式里，很多对象由每一个对象对其下家的引用而连接起来形成一条链。
* 责任链每一个节点，都可以继续调用下一个节点，也可以阻止流程继续执行。

![](http://ww2.sinaimg.cn/large/83e1667djw1f8wspav7yyj21ik0qak6h.jpg)

4.Struts2中在struts-default.xml文件中声明了所有的拦截器。  
5.Struts2框架默认使用的是defaultStack这个拦截器栈。    
6.在这个拦截器栈中使用了18个拦截器。简单说，Struts2框架在默认情况下，加载了18个拦截器。

***

## <center>自定义拦截器</center>


通过使用拦截器可以控制action的访问。例如，权限操作。  

**步骤：**  

**1.创建一个Interceptor，可以自定义一个类实现Interceptor接口**

在这个接口中有三个方法：init、destory、intercept，intercept方法是真正拦截的方法。    
在intercept方法中如果要向下继续执行，通过其参数ActionInvocation调用它的invoke()方法就可以。  
(也可继承MethodFilterInterceptor，重写doIntercept方法)
{: .notice}			
		
**2.在struts.xml文件中声明一个Interceptor**    

按照struts-default.xml文件中声明的方式：  

```xml
<interceptors>
	<interceptor name="" class=""/>
</interceptors>
```


**3.在(struts.xml的)action中指定使用哪些拦截器**  

注意:只要显示声明使用了一个拦截器，那么默认的拦截器就不在加载。
{: .notice}

```xml
<interceptor-ref name=""/>
```


**示例程序：** 

```java
public class MyIntercepter implements Interceptor{

	@Override
	public void destroy() {
		System.out.println("my intercptor destroy");
	}
	@Override
	public void init() {
		System.out.println("my intercptor init");
	}
	@Override
	public String intercept(ActionInvocation ai) throws Exception {
		System.out.println("my interceptor works");
		//return ai.invoke(); // 放行
		return "LOGIN";  //"login"
	}
}
```


```xml
<struts>
    <package name="default" namespace="/" extends="struts-default">
        <interceptors>
        	<interceptor name="myIntercepter" class="cn.xsw.test.MyIntercepter"></interceptor>
        </interceptors>
        <action name="test"  class="cn.xsw.test.Test">  
        	<result>/index.jsp</result> 
        	<result name="LOGIN">/login.jsp</result> 
        	<interceptor-ref name="myIntercepter"/>
        	<interceptor-ref name="defaultStack"/>
        </action>
    </package>
</struts>
```

**拦截器栈：**  

```xml
<struts>
	<package name="default" namespace="/" extends="struts-default">
		<interceptors>	
			<interceptor name="my" class="cn.xsw.test.MyIntercepter"/>
			<interceptor-stack name="myStack"><!--拦截器栈-->
				<interceptor-ref name="my"/>
				<interceptor-ref name="defaultStack" />
			</interceptor-stack>
		</interceptors>	

		<action name="test"  class="cn.xsw.test.Test">  
        	<result>/index.jsp</result> 
        	<result name="LOGIN">/login.jsp</result> 
        	<interceptor-ref name="myStack" /><!--引用拦截器栈-->
        </action>
	</package>
</struts>
```

***

## <center>拦截器原理</center>


**源代码执行流程:**

1.在StrutsPrepareAndExecuteFilter中查找，在doFilter方法内有：  
 `execute.executeAction (request, response, mapping)` 执行Action操作。
	
2.在executeAction执行过程中会访问Dispatcher类中的serviceAction，在这个方法中会创建Action的代理对象。

```java
ActionProxy proxy = config.getContainer().getInstance(ActionProxyFactory.class).createActionProxy(namespace, name, method, extraContext, true, false);
```


3.查看ActionInvocation，查看其实现类 DefaultActionInvocation。在其invoke方法中：  

```java
if (interceptors.hasNext()) {//判断是否有下一个拦截器
	final InterceptorMapping interceptor = interceptors.next(); //得到一个拦截器
	String interceptorMsg = "interceptor: " + interceptor.getName();
	UtilTimerStack.push(interceptorMsg);
	try {
			resultCode = interceptor.getInterceptor().intercept(DefaultActionInvocation.this); 
			//调用得到的拦截器的拦截方法.将本类对象传递到了拦截器中。
		}
	finally {
		UtilTimerStack.pop(interceptorMsg);
	}
} 
```

通过源代码分析，发现在DefaultActionInvocation中就是通过递归完成所有的拦截调用操作。  

***

## <center>Interceptor与Filter区别</center>

1.Interceptor是基于Java反射机制的，而Filter是基于函数回调的。  
2.Filter依赖于Servlet容器，而Interceptor不依赖于Servlet容器。  
3.Interceptor只能对Action请求起作用，而Filter则可以对几乎所有请求起作用。  
4.Interceptor可以访问Action上下文、值栈里的对象，而Filter不能。  
5.在Action的生命周期中，Interceptor可以多次调用，而Filter只能在容器初始化时被调用一次。
{: .notice}
