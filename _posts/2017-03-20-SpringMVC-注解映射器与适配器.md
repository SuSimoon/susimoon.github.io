---
layout: post
title:  "SpringMVC-注解映射器与适配器"
date:   2017-03-20
excerpt: "本篇介绍了SpringMVC中的注解处理器适配器的配置"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">注解映射器</a>  
><a href="#2">注解适配器</a>      

***

<a name="1"></a>

## <center>注解映射器</center>  

spring3.1之前默认加载映射器是：  
org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping  
3.1之后要使用：  
org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping  

**在springmvc.xml中配置RequestMappingHandlerMapping：**  
需要在Handler中使用`@controller`标识此类是一个控制器。  
使用`@requestMapping`指定Handler方法所对应的url。  
{: .notice} 

```xml
<!-- 注解处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
```

***

<a name="2"></a>

## <center>注解适配器</center>  

spring3.1之前默认加载映射器是：  
org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter  
3.1之后要使用：
org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter

RequestMappingHandlerAdapter不要求Handler实现任何接口。  
它需要和RequestMappingHandlerMapping注解映射器配对使用，主要解析Handler方法中的形参。
{: .notice} 

```xml
<!-- 注解适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter" />
```

***


## <center>程序示例</center> 


###	注解开发Hanlder

```java
@Controller
public class ItemController3 {
	
	@RequestMapping("/queryItems") //@RequestMapping中url建议和方法名一致，方便开发维护
	public ModelAndView queryItems(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
		List<Items> itemsList = new ArrayList<Items>();
		
		Items items_1 = new Items();
		items_1.setName("Pen");
		items_1.setPrice(10f);
		
		Items items_2 = new Items();
		items_2.setName("Paper");
		items_2.setPrice(0.5f);
		
		itemsList.add(items_1);
		itemsList.add(items_2);
		
		ModelAndView modelAndView = new ModelAndView();
		//将数据填充到request域
		modelAndView.addObject("itemsList", itemsList);
		//指定转发的页面
		modelAndView.setViewName("/WEB-INF/jsp/itemsList.jsp");
		
		return modelAndView;
	}
}
```


###	配置Hanlder

单个配置形式：

```xml
<!-- 配置Handler -->
<bean class="cn.xsw.springmvc.controller.ItemController3"/>
```


扫描形式：

```xml
<!-- 使用spring组件扫描 -->
<context:component-scan base-package="cn.xsw.springmvc.controller" />
```

注：组件扫描可以扫描@Controller、@Service、@component、@Repsitory
{: .notice} 

