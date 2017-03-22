---
layout: post
title:  "SpringMVC-@RequestMapping"
date:   2017-03-21
excerpt: "本篇介绍了SpringMVC中@RequestMapping的用法"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">设置方法对应的url</a>  
><a href="#2">窄化请求映射</a>  
><a href="#3">限制http请求的方法</a>     

***

<a name="1"></a>

## <center>设置方法对应的url</center> 

一个方法对应一个url。  

```java
@RequestMapping("/queryItems") //<---------
	public ModelAndView queryItems() throws Exception {
		//调用service查询商品列表
		List<ItemsCustom> itemsList = itemsService.findItemsList(null);
		ModelAndView modelAndView = new ModelAndView();
		modelAndView.addObject("itemsList",itemsList);
		modelAndView.setViewName("itemsList");
		return modelAndView;
	}
```

***

<a name="2"></a>

## <center>窄化请求映射</center> 

在class上定义根路径。  
好处：更新规范系统的url，避免url冲突。

```java
//定义url根路径，访问：根路径+方法的url
@RequestMapping("/items")
public class ItemsController {
}
```



***

<a name="3"></a>

## <center>限制http请求的方法</center> 


通过requestMapping限制url请求的http方法，例如限制请求必须是post，如果get请求就抛出异常。  

```java
//限制使用get方法
@RequestMapping(value="/editItems",method=RequestMethod.GET)
```

