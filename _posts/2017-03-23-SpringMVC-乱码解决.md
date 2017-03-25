---
layout: post
title:  "SpringMVC-乱码解决"
date:   2017-03-22
excerpt: "本篇介绍了SpringMVC中Controller方法的返回值"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">返回ModelAndView</a>  
><a href="#2">返回字符串</a>  
><a href="#3">返回void</a>     

***

<a name="1"></a>

## <center>post请求乱码</center> 


在web.xml中加入：

```xml
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
</filter>
<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

***

<a name="1"></a>

## <center>get请求乱码</center> 

对于get请求中文参数出现乱码解决方法有两个：

1.修改tomcat配置文件添加编码与工程编码一致，如下：

```xml
<Connector URIEncoding="utf-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```

另外一种方法是对参数进行重新编码：

```java
String userName = 
new String(request.getParamter("userName").getBytes("ISO8859-1"),"utf-8");
```

ISO8859-1是tomcat默认编码，需要将tomcat编码后的内容按utf-8编码。



