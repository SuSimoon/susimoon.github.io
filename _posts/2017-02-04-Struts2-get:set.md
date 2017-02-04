---
layout: post
title:  "Struts2-get/set"
date:   2017-02-04
excerpt: "本篇介绍了Struts2中的get、set方法的作用"
tag:
- Java 
- Struts2
- get/set
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

## <center>Struts2中的get、set方法作用</center>

在Struts2中，客户端和服务器之间的数据传输全部要用到get、set方法：    

1.用set方法可以将表单中的值存入Action类。    
2.通过Struts2.0标签，调用get方法将Action类中的结果数据显示在页面上。
{: .notice}  

1.当在业务逻辑中需要用到页面传过来的值的时候，只要保证Action中属性的set方法和页面中的name属性值`<s:textfield name=""/>`相同即可。（去掉set，方法名大写变小写）  
2.当在显示页面中需要用到业务逻辑中的返回值时，只要保证页面标签的value值`<s:property value=""/>`和Action中属性的get方法形同即可。
{: .notice} 