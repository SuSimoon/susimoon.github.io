---
layout: post
title:  "Struts2-Action"
date:   2017-01-24
excerpt: "本篇介绍了Struts2中Action类的创建方式、访问方式、处理请求参数的方式以及如何获取Servlet API"
tag:
- Struts2
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---



><a href="#1">Action类的创建方式</a>     
><a href="#2">Action的访问方式</a>     
><a href="#3">Action处理请求参数</a>     
><a href="#4">获取Servlet API</a>          

***

<a name="1"></a>

## <center>Action类的创建方式</center> 


### 1.创建一个POJO类(简单的Java对象)  

优点:无耦合    
缺点:所有工作都要自己实现    


### 2.实现Action接口

优点:耦合低，提供了五种结果视图，定义了一个行为方法  
缺点:所以工作都要自己实现  

```java
1.public static final String SUCCESS = "success";  // 数据处理成功（成功页面）
2.public static final String NONE = "none";        // 页面不跳转，与return null; 效果一样
3.public static final String ERROR = "error";      // 数据处理发送错误 (错误页面)
4.public static final String INPUT = "input";      // 用户输入数据有误，通常用于表单数据校验（输入页面）
5.public static final String LOGIN = "login";      // 主要权限认证 (登陆页面)
```

### 3.继承ActionSupport类

ActionSupport类实现了Action接口，是Action的一个实现类。  
优点:表单校验、错误信息设置、读取国际化信息，这三个功能都支持(在开发中应用的比较多)  
缺点:耦合度高  

***  

<a name="2"></a>

## <center>2.Action的访问方式</center>  


### **1.设置method的值来确定方法**

```xml
<action name="book_add" class="cn.xsw.action.BookAction" method="add"></action>
当访问的是book_add,这时就会调用BookAction类中的add方法。			
```


### **2.使用通配符来简化配置**

```xml
<action name="*_*" class="cn.xsw.action.{1}Action" method="{2}"></action>

当访问Book_add时，这时的路径是Book_add,那么对于struts.xml文件中：
第一个*就是  Book
第二个*就是  add
对于{1}Action --> BookAction
对于method={2} --> method=add
```

使用通配符来配置注意事项:  
	1.必须定义一个统一的命名规范。  
	2.不建议使用过多的通配符，阅读不方便。
{: .notice}


### **3.动态方法调用(了解)**

```xml
<action name="book" class="cn.xsw.action.BookAction"></action>

访问时路径: http://localhost/struts2/book!add 就访问到了BookAction类中的add方法。
对于book!add 这就是动态方法调用。(!后是方法名，没有配置但能访问add方法)
```

注意：struts2框架支持动态方法调用，是因为在default.properties配置文件中设置了动态方法调用为true。  
	struts.enable.DynamicMethodInvocation = true   
	有的版本是false，可在struts.xml设置
{: .notice}

***

<a name="3"></a>

## <center>3.Action处理请求参数</center> 

>1.属性驱动  
>　　*　自身作为model对象  
>　　*　创建独立model对象  
>2.模型驱动  


### 属性驱动  

**`1.自身作为model对象`**  

Action本身作为model对象，通过成员setter封装。  
Action封装请求参数，不会存在线程安全问题，因为每一次请求，都是一个新的Action。  
优点:比较简单。  
缺点:需要单独定义JavaBean，将action中属性copy到JavaBean中。	

```java
public class Login1Action extends ActionSupport {
	private String username;

	public void setUsername(String username) {
		this.username = username;
	}
	public String execute() throws Exception {
		...
	}
}
```

**`2.创建独立model对象`**

创建独立model对象，页面通过ognl表达式封装。  
private User user; 提供get/set方法。    
在页面上使用ognl来描述 `<input type="text" name="user.username">`   

优点:简单，解决了第一种封装的问题。  
缺点:在页面上使用了ognl表达式，页面不通用了。  

```java
public class User {
	private String name;

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

```java
public class Test {
	private User user;
	
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
	public String execute() {
		...
	} 
}
```

### 模型驱动  

优点:解决了属性驱动存在的问题。  
缺点:一次只能封装一个model对象。  

步骤:  
	1.让Action类实现ModelDriven接口。  
	2.重写getModel方法。  
	3.在action中实例化(new)一个model对象，让getModel方法返回这个对象。  
	(action实现了ModelDriven接口时，会调用getModel方法，该方法返回user对象，即可调用user对象中的方法)。
{: .notice}
	

```java
public class Test extends ActionSupport implements ModelDriven<User>{
	private User user = new User();
	
	@Override
	public User getModel() {
		return user;
	}
	public String execute() {
		...
	}
}
```


*** 

<a name="4"></a>

## <center>获取Servlet API(访问Web元素)</center>

>1.获取Map类型  
>2.获取真实类型    

## <center>获取Map类型</center> 

### 通过ActionContext获取(依赖Struts2) 


1.获取ActionContext对象  
	ActionContext context=ActionContext.getContext();  

2.获取Servlet API  
	注意:通过ActionContext获取的不是真正的Servlet API，而是一个Map集合。(在Stack Context中以map形式存储)  


```java	
1.context.getApplication();
2.context.getSession();
3.context.getParameter(); // 得到的就相当于request.getParameterMap();
4.context.put(String,Object); // 相当于request.setAttribute(String,String);
```


```java
public class Test extends ActionSupport{
	
	private Map request;
	private Map session;
	private Map application;
	
	public Test() {
		request = (Map) ActionContext.getContext().get("request");
		session = ActionContext.getContext().getSession();
		application = ActionContext.getContext().getApplication();
	}
	
	public String execute() {
		request.put("r1", "r1");
		session.put("s1", "s1");
		application.put("a1", "a1");
		return SUCCESS;
	}
}
```

```html
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
  </head>
  
  <body>
    <s:property value="#request.r1"/> | <%=request.getAttribute("r1") %><br/>
    <s:property value="#session.s1"/> | <%=session.getAttribute("s1") %><br/>
    <s:property value="#application.a1"/> | <%=application.getAttribute("a1") %><br/>
    <!-- 可通过HttpRequest等获取-->
    <s:debug></s:debug>
  </body>
</html>
```


### 通过注入方式获取(IOC)  

实现接口：  

RequestAware  
SessionAware  
ApplicationAware
{: .notice}

```java
public class Test extends ActionSupport implements RequestAware,SessionAware, ApplicationAware {
	
	private Map<String, Object> request;
	private Map<String, Object> session;
	private Map<String, Object> application;

	@Override
	public void setRequest(Map<String, Object> request) {
		this.request = request;
	}

	@Override
	public void setSession(Map<String, Object> session) {
		this.session = session;
	}

	@Override
	public void setApplication(Map<String, Object> application) {
		this.application = application;
	}

	public String execute() {
		request.put("r1", "r1");
		session.put("s1", "s1");
		application.put("a1", "a1");
		return SUCCESS; 
	}
}
```


## <center>获取真实类型</center> 


### 通过ServletActionContext获取(依赖Struts2)

ServletActionContext中方法都是static类型。  

ServletActionContext.getRequest();  
ServletActionContext.getResponse();  
ServletActionContext.getPageContext();  
{: .notice}


```java
package cn.itcast.action;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;

import org.apache.struts2.ServletActionContext;
import org.apache.struts2.interceptor.ServletRequestAware;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;

//获取servlet api  通过ServletActionContext获取
public class Test extends ActionSupport {

	@Override 
	public String execute() {
		HttpServletRequest request = ServletActionContext.getRequest();
		...
		return SUCCESS;
	}
}
```


### 通过注入方式获取(IOC)

依赖注入的方式，这种方式是真正的获取到了Servlet API，比较常用。  

1.要求action类必须实现指定接口。      

ServletContextAware:  注入ServletContext对象  
ServletRequestAware:  注入request对象  
ServletResponseAware: 注入response对象
{: .notice}

2.重写接口中的方法。    
3.声明一个web对象，使用接口中的方法的参数对声明的web对象赋值。  	

```java
public class Test extends ActionSupport implements
		ServletRequestAware {

	private HttpServletRequest request; //注入
	@Override
	public void setServletRequest(HttpServletRequest request) {
		this.request = request;
	}
	
	public String execute() throws Exception {
		...
		return null;
	}
}
```

分析其实现：  
是使用struts2中的一个interceptor完成的。  
`<interceptor name="servletConfig" class="org.apache.struts2.interceptor.ServletConfigInterceptor"/>`

```java
if (action instanceof ServletRequestAware) { // 判断action是否实现了ServletRequestAware接口
	HttpServletRequest request = (HttpServletRequest) context.get(HTTP_REQUEST); // 得到request对象
	((ServletRequestAware) action).setServletRequest(request); // 将request对象通过action中重写的方法注入
}		
```


注：通常使用注入方式
{: .notice}




