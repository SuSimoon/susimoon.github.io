---
layout: post
title:  "SpringMVC-参数绑定"
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

## <center>默认支持的参数类型</center> 


处理器形参中添加如下类型的参数，处理适配器会默认识别并进行赋值。  

### 简单类型

可以绑定整型、字符串、单精/双精度、日期、布尔型。
{: .notice} 

### HttpServletRequest  

通过request对象获取请求信息  
{: .notice} 

### HttpServletResponse  

通过response处理响应信息  
{: .notice} 

### HttpSession  

通过session对象得到session中存放的对象
{: .notice} 

### Model  

通过model向页面传递数据，例如：

```java
//调用service查询商品信息
Items item = itemService.findItemById(id);
model.addAttribute("item", item);
```


***

<a name="2"></a>

## <center>`@RequestParam`</center> 

如果request请求的参数名和controller方法的形参数名称一致，适配器自动进行参数绑定。  
例如：在地址栏中输入参数是id=1，则editItems方法会接收到参数。

```java
@RequestMapping(value="/editItems",method=RequestMethod.GET)
public ModelAndView editItems(Integer id) throws Exception {  //<------
	ModelAndView modelAndView = new ModelAndView();
	ItemsCustom itemsCustom = itemsService.findItemsById(id);
	modelAndView.addObject("item", itemsCustom);
	modelAndView.setViewName("editItem");
	return modelAndView;
}
```

如果不一致，可以通过`@RequestParam`指定request请求的参数名绑定到哪个方法形参上。  
例如：在地址栏中输入参数是item_id=2，则editItems方法也会接收到参数。  

```java
@RequestMapping(value="/editItems",method=RequestMethod.GET)
public ModelAndView editItems(@RequestParam(value="item_id")Integer id) throws Exception {  //<------
	
	....
}
```


对于必须要传的参数，通过属性required设置为true，如果不传此参数则报错。  
对于有些参数如果不传入，还需要设置默认值，使用属性defaultvalue设置默认值。
{: .notice} 


```java
public ModelAndView editItems(@RequestParam(value="item_id",required=true, defaultValue="1")Integer id) throws Exception {
		....
	}
```


***


<a name="3"></a>

## <center>简单pojo类型</center> 






