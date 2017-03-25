---
layout: post
title:  "SpringMVC-@ModelAttribute"
date:   2017-03-24
excerpt: "本篇介绍了SpringMVC中用@ ModelAttribute来进行数据回显"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  
    

***


## <center>@ ModelAttribute数据回显</center> 

使用`@ModelAttribute`，作用于将请求POJO数据放到Model中回显到页面。  
在ModelAttribute方法指定的名称填充到Model中的key，在页面中通过key取数据。

```java
public String editItem(@ModelAttribute(value="item") Item item) {
	...
}
```

## <center> @ModelAttribute 将方法返回值传到页面</center>

使用`@ModelAttribute`将公用的取数据的方法返回值传到页面，不用在每一个Controller方法通过Model将数据传到页面。


```java
//单独将商品类型的方法提出来，将方法返回值填充到request，在页面显示
@ModelAttribute("itemsType")
public Map<String, String> getItemsType()throws Exception{
	HashMap<String, String> itemsType = new HashMap<String,String>();
	itemsType.put("001", "数码");
	itemsType.put("002", "服装");
	return itemsType;
}
```

```html
<select>
	<c:forEach items="${itemsType}" var="item">
		<option value="${item.key }">${item.value }</option>
	</c:forEach>
</select>
```
