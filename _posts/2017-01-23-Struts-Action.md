---
layout: post
title:  "Struts2-Action"
date:   2017-01-24
excerpt: "本篇介绍了Struts2的Action"
tag:
- Java 
- Struts2
- SSH框架
comments: false
---



>1.Action类的创建方式    
>2.Action的访问方式   
>3.Action处理请求参数      


***  


## <center>Action的访问方式</center>  


* **1.通过设置method的值，来确定访问action类中的哪一个方法**

```xml
<action name="book_add" class="cn.xsw.action.BookAction" method="add"></action>
当访问的是book_add,这时就会调用BookAction类中的add方法。			
```


* **2.使用通配符来简化配置**

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


* **3.动态方法调用(了解)**

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

## <center>Action处理请求参数</center> 

>1.属性驱动  
>　　*自身作为model对象  
>　　*创建独立model对象  
>2.模型驱动  


### 属性驱动  

* 自身作为model对象 

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
		if ("tom".equals(username) ) {
			return "SUCCESS";
		} else {
			return "FAIL";
		}
	}
}
```

* 创建独立model对象

创建独立model对象，页面通过ognl表达式封装。







