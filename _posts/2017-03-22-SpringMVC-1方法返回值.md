---
layout: post
title:  "SpringMVC-Controller方法返回值"
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

## <center>返回ModelAndView</center> 

```java
@RequestMapping(value="/editItems",method=RequestMethod.GET)
public ModelAndView editItems() throws Exception {
	ModelAndView modelAndView = new ModelAndView();
	ItemsCustom itemsCustom = itemsService.findItemsById(1);
	modelAndView.addObject("item", itemsCustom);
	//指定逻辑视图名
	modelAndView.setViewName("editItem");
	return modelAndView;
}
```


***

<a name="2"></a>

## <center>返回字符串</center> 

如果controller方法返回jsp页面，可以简单将方法返回值类型定义为字符串，最终返回逻辑视图名。

```java
//字符串是逻辑视图名，model作用是将数据填充到request域中并在页面展示
@RequestMapping(value="/editItems",method=RequestMethod.GET)
public String editItems(Model model) throws Exception {   //<-----
	ItemsCustom itemsCustom = itemsService.findItemsById(1);
	model.addAttribute("item", itemsCustom);
	return "editItem";   //<-----
}
```

### redirect重定向

如果方法重定向到另一个url，方法返回值为`“redirect:url`路径”。  
使用redirect进行重定向，request数据无法共享(例如传入id值无法接收)，url地址栏会发生变化。  
变为：http://localhost:8080/SpringMVC/items/queryItems.action  
{: .notice} 

### forward转发

使用forward进行请求转发，方法返回值为`“forward:url`路径”。  
request数据可以共享，url地址栏不会。  
变为：http://localhost:8080/SpringMVC/items/editItemSubmit.action  
{: .notice} 

```java
//商品修改提交
@RequestMapping("/editItemSubmit")
public String editItemSubmit() throws Exception { 
	
	//return "redirect:queryItems.action"; // 不需要写根路径
	return "forward:queryItems.action";
}
```

***

<a name="3"></a>

## <center>返回void</center> 

使用此方法，容易输出json、xml格式的数据，通过response指定响应结果。  

```java
@RequestMapping(value="/editItems",method=RequestMethod.GET)
public void editItems(HttpServletRequest request, HttpServletResponse response) throws Exception {   //<-----
	ItemsCustom itemsCustom = itemsService.findItemsById(1);
	request.setAttribute("item", itemsCustom);
	//request转向页面，需要指定页面的完整路径
	request.getRequestDispatcher("/WEB-INF/jsp/editItem.jsp").forward(request, response);   //<-----
}
```
