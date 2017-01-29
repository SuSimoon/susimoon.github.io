---
layout: post
title:  "Struts2-Exception"
date:   2017-01-28
excerpt: "本篇介绍了Struts2中提供的异常处理"
tag:
- Java 
- Struts2
- Exception
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
--- 



对于action中的操作，出现问题，直接抛出自定义异常。  

## <center>方式</center>  

在struts.xml文件中定义异常的映射，可以让特定的异常，跳转到自定的页面。  

```xml
<global-exception-mappings>
	<exception-mapping result="error" exception="cn.xsw.exception.FindByIdException">
	</exception-mapping>
</global-exception-mappings>
```

同时可以定义一个全局的抛出异常后要跳转的页面

```xml
<global-results>
	<result name="error">/error.jsp</result>
</global-results>
```


## <center>原理</center>  

struts2默认加载的18个拦截器的第一个是exception这个拦截器。  
它没有做任何操作，直接放行，只是它将invocation.invoke()操作使用try-catch进行了处理。
其它的拦截器或是action，只要向外抛出异常，exception拦截器就会将其捕获。






