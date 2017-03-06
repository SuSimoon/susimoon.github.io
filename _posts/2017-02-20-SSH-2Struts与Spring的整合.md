---
layout: post
title:  "SSH-Spring与Struts的整合"
date:   2017-02-20
excerpt: "本篇介绍了SSH框架中Struts与Spring整合的两种方式"
tag:
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

***


## <center>方式一：Struts2自己管理Action</center> 


Struts2框架自动创建Action的类，在struts.xml中配置。  

```xml
<action name="book_*" class="cn.xsw.action.BookAction" method="{1}">
```

***

## <center>方式二：Action交给Spring管理</center> 

在Spring中管理Action类，可以对其进行AOP开发，并能统一的管理。

**1.在Struts配置文件struts.xml中，在`<action>`标签上通过一个伪类名方式进行配置:**

```xml	
<action name="book_*" class="bookAction(交给spring创建，名称要一样)" method="{1}"></action>
```

**2.在spring的配置文件applicationContext.xml中:**    

```xml
<!-- 配置Action -->
<bean id="bookAction" class="cn.xsw.action.BookAction" scope=”prototype”></bean>
```

注：Action交给Spring管理时一定要配置`scope=”prototype”`  
Struts中是多例模式，而Spring中默认是单例模式，会出现并发访问问题，所以要改成多例模式。
{: .notice}


### 自动注入

在传统的Spring中：  
若想在Web层调用Bean中的方法，需要获得WebApplicationContext对象并通过getBean方法来实现。  

在实际开发中:  
引入了jar包struts2-spring-plugin-2.3.15.3.jar，其中有配置文件struts-plugin.xml。  
其中开启常量:`<constant name="struts.objectFactory" value="spring" />`  
引发另一个常量的执行:`struts.objectFactory.spring.autoWire = name`即Spring的工厂类按照名称自动注入。  
所以可以直接调用注入对象的方法。

```java
public class BookAction extends ActionSupport implements ModelDriven<Book>{
	
	private BookService bookService; 
	
	public void setBookService(BookService bookService) { 
		this.bookService = bookService;
	}

	// 模型驱动类
	private Book book = new Book();
	public Book getModel() {
		return book;
	}

	// 处理请求的方法:
	public String add(){
		//直接调用service层方法，不需要获得WebApplicationContext对象
		bookService.add(book); 
		return NONE;
	}
}
```


