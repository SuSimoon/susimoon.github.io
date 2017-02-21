---
layout: post
title:  "Spring-整合Web开发"
date:   2017-02-17
excerpt: "本篇介绍了Spring如何整合web开发"
tag:
- Java 
- Spring
- Web
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  
  
   
***


## <center>Spring整合web开发</center> 


在整合Servlet和Spring时，每次执行Servlet的时候都会加载一次Spring的配置和环境。  
若采用在Servlet的init方法中加载Spring配置文件的方法，那么只有当前Servlet可用，其它的Servlet无法使用。  

### 解决办法

ServletContext对象是全局的对象，服务器启动的时候创建。  
可将加载的信息内容放到ServletContext中，在创建ServletContext的时候就加载Spring的环境。  
ServletContextListener:用于监听ServletContext对象的创建和销毁的。
{: .notice}

### 步骤

1.**导入jar包:**`spring-web-3.2.0.RELEASE.jar`


2.**在web.xml中配置:**

```xml
<listener>
	<!--ContextLoaderListener实现了ServletContextListener接口-->
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<!-- 默认加载WEB-INF下的配置，需要修改配置文件的路径 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

3.**修改程序的代码:**
	
```java
//原来的方式：
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

//修改后：从ServletContext中获取信息
WebApplicationContext applicationContext = WebApplicationContextUtils.getWebApplicationContext(getServletContext());
//或:
WebApplicationContext applicationContext = (WebApplicationContext) getServletContext().getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
```

