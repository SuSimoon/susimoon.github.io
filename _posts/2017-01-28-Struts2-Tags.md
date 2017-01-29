---
layout: post
title:  "Struts2-Tags"
date:   2017-01-28
excerpt: "本篇介绍了Struts2中标签的使用方式，包括property、set、bean、include、fielderror
、iterator、ui"
tag:
- Java 
- Struts2
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

>property  
>set  
>bean  
>include  
>fielderror  
>iterator  
>ui  



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

***

## <center>bean</center>

```java
public class User {
	private String username;
	
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	@Override
	public String toString() {
		return "User [username=" + username + "]";
	}
}
```

**不设置var：**  

debug要放入到bean标签内部才能看到value stack中User对象入栈顶。  
bean结束后User对象会消失。

```html
<s:bean name="cn.xsw.test.User" var="user"><!--User对象压入栈顶-->
	<s:param name="username" value="'tom'"></s:param>
	<s:debug></s:debug>
<s:property value="username"/>
</s:bean>
```

**设置var：**  

若希望bean结束后User对象还能够使用需要var，会放入action context中。

```html
<s:bean name="cn.xsw.test.User" var="user"><!--要定义变量user才能存入到Stack Context-->
	<s:param name="username" value="'tom'"></s:param><!--User类中的username设值-->
</s:bean>

<s:property value="#user.username"/>
```



***


## <center>include</center>

尽量少使用，含有中文字符时容易出问题。  

```html
1.<s:include value="/_include1.html"></s:include>  

2.说明%用法:<!--%{}表示当作ognl表达式而非字符串-->
<s:set var="incPage" value="%{'/_include1.html'}" />
<s:include value="%{#incPage}"></s:include>
```


***

## <center>fielderror</center>

```java
this.addFieldError("test", "wrong");
```

```html
<s:fielderror />
```


***

## <center>iterator</center>  


**1.遍历集合：**

```html
<s:iterator value="{1,2,3}">
	<s:property/>
</s:iterator>
```


**2.自定义变量：**

```html
<s:iterator value="{'aaa','bbb','ccc'}" var='x'>
	<s:property value="#x.toUpperCase()"/>
</s:iterator>
```

**3.status：**

```html
<s:iterator value="{'aaa', 'bbb', 'ccc'}" status="status">
	<s:property/> | <!-- 取出value中每个元素 --> 
	遍历过的元素总数：<s:property value="#status.count"/> |
	遍历过的元素索引：<s:property value="#status.index"/> |
	当前是偶数？：<s:property value="#status.even"/> |
	当前是奇数？：<s:property value="#status.odd"/> |
	是第一个元素吗？：<s:property value="#status.first"/> |
	是最后一个元素吗？：<s:property value="#status.last"/>
	<br />
</s:iterator>
```

**4.遍历map：**  

表示map需要有#。  

```html
<s:iterator value="#{1:'a', 2:'b', 3:'c'}" >
	<s:property value="key"/> | <s:property value="value"/><br />
</s:iterator>

<s:iterator value="#{1:'a', 2:'b', 3:'c'}" var="x">
	<s:property value="#x.key"/> | <s:property value="#x.value"/><br />
</s:iterator>
```


***


## <center>ui</center> 

theme默认为xhtml。要修改需要在struts.xml中设置：  
`<constant name="struts.ui.theme" value="simple"></constant>`  

fielderror前面有个小点的原因是ul和li生成的。  
去掉fielderror前面的点的方式：

**1.添加class，去掉ul和li前面的点**

```html
<html>
  <head>
  <style type="text/css">
  .formFieldError ul li {
  	list-style-type: none
  }
  </style>
  </head>
  
  <body>
  	<s:form>
  		<div class="formFieldError"><!--要设置class-->
  			<s:fielderror/>
  		</div>
  	</s:form>
  </body>
</html>
```


**2.直接修改错误信息的class样式**  

查看前台页面的源代码，根据错误信息的class，修改其css样式来覆盖原来的class即可。  
即若页面显示：    

```html
<ul class="errorMessage">
	<li><span>wrong</span></li>
</ul>
```

则修改css：  

```html
<style type="text/css">
  .errorMessage ul li {
  	list-style-type: none
  }
</style>
```

**3.覆盖fielderror.ftl**  


在default.properties中有`struts.ui.templateDir=template`，  
就是说只要是template下面的内容都被认为是theme，因此可以在src下建立：  
template文件夹-simple文件夹-fielderror.ftl用来覆盖struts2中自己的template文件。  

```
<#--
/*
 * $Id: fielderror.ftl 722375 2008-12-02 05:19:57Z wesw $
 *
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements.  See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership.  The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License.  You may obtain a copy of the License at
 *
 *  http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing,
 * software distributed under the License is distributed on an
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
 * KIND, either express or implied.  See the License for the
 * specific language governing permissions and limitations
 * under the License.
 */
-->
<#if fieldErrors??><#t/>
<#assign eKeys = fieldErrors.keySet()><#t/>
<#assign eKeysSize = eKeys.size()><#t/>
<#assign doneStartUlTag=false><#t/>
<#assign doneEndUlTag=false><#t/>
<#assign haveMatchedErrorField=false><#t/>
<#if (fieldErrorFieldNames?size > 0) ><#t/>
	<#list fieldErrorFieldNames as fieldErrorFieldName><#t/>
		<#list eKeys as eKey><#t/>
		<#if (eKey = fieldErrorFieldName)><#t/>
			<#assign haveMatchedErrorField=true><#t/>
			<#assign eValue = fieldErrors[fieldErrorFieldName]><#t/>
			<#if (haveMatchedErrorField && (!doneStartUlTag))><#t/>
				<ul<#rt/>
<#if parameters.cssClass??>
 class="${parameters.cssClass?html}"<#rt/>
<#else>
 class="errorMessage"<#rt/>
</#if>
<#if parameters.cssStyle??>
 style="${parameters.cssStyle?html}"<#rt/>
</#if>
>
				<#assign doneStartUlTag=true><#t/>
			</#if><#t/>
			<#list eValue as eEachValue><#t/>
				<li><span>${eEachValue}</span></li>
			</#list><#t/>			
		</#if><#t/>
		</#list><#t/>
	</#list><#t/>
	<#if (haveMatchedErrorField && (!doneEndUlTag))><#t/>
		</ul>
		<#assign doneEndUlTag=true><#t/>
	</#if><#t/>
<#else><#t/>	
	<#if (eKeysSize > 0)><#t/>
		
			<#list eKeys as eKey><#t/>
				<#assign eValue = fieldErrors[eKey]><#t/>
				<#list eValue as eEachValue><#t/>
					<span>${eEachValue}</span>
				</#list><#t/>
			</#list><#t/>
		
	</#if><#t/>
</#if><#t/>
</#if><#t/>
```


**4.自定义theme** 

```xml
<constant name="struts.ui.theme" value="mytheme"></constant>
```

以在src下建立mytheme文件夹-simple文件夹-fielderror.ftl，ielderror.ftl同上。



***
ps：看到var，就是放到了Stack Context中，就可以通过#来取值。
{: .notice}

