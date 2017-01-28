---
layout: post
title:  "Struts2-Tags"
date:   2017-01-28
excerpt: "本篇介绍了Struts2中标签的使用方式"
tag:
- Java 
- Struts2
- SSH框架
comments: false
---   

***

## <center>property</center>  

**property判断是字符串还是ognl表达式的依据：**      
value的类型是Object，都会解析为ognl表达式。    
想要解析成普通的字符串需要加单引号。
{: .notice}    

escape默认是true，会解析成普通字符串。   
escape默认是false，会解析成html。  
{: .notice} 

```html
1.property: <s:property value="username"/> 
2.property 取值为字符串: <s:property value="'username'"/> 
3.property 设定默认值: <s:property value="admin" default="管理员"/><!--取不到用默认值-->
4.property 设定HTML: <s:property value="'<hr/>'" escape="false"/>
```


*** 

## <center>set</center> 

该标签的id和那么属性都已经废弃。  
scope的默认值为action，即request 和 ActionContext。  
可以取的值有application、session、request、page和action。
{: .notice}


```html
1.set 设定值: <s:set var="adminName" value="username" />
2.set 从request取值: <s:property value="#request.adminName" />
3.set 从ActionContext取值: <s:property value="#adminName" />

4.set 设定范围不是ActionContext时: <s:set var="adminPassword" value="password" scope="session"/>
5.set 使用#不能取到值: <s:property value="#adminPassword"/>
6.set 应从相应范围取值: <s:property value="#session.adminPassword"/>
```


## <center>bean</center>













