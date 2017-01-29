---
layout: post
title:  "Struts2-OGNL"
date:   2017-01-27
excerpt: "本篇介绍了Struts2中OGNL的访问方式"
tag:
- Java 
- Struts2
- OGNL
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

*** 

>OGNL的访问方式

### 访问值栈中的action的普通属性   

直接访问值栈中的属性即可。   

```java
public class Test extends ActionSupport {
	private String username;
	
	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String execute() {
		return "success";
	}
}
```


```xml
<s:property value="username"/>
```


### 访问值栈中action的普通方法

```java
public class Test extends ActionSupport {
	private String username;
	
	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String execute() {
		return "success";
	}

	public String printUsername() { //普通方法
		return username;
	}
}
```

```xml
<s:property value="printUsername()"/>
```

***

### 访问值栈中对象的普通属性(get、set方法)  

`例子程序：`

```java
public class User {
	private String username = "tom";
	
	public User() {
		System.out.println("user constructor");
	}
	public User(String username) {
		super();
		this.username = username;
	}
	
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
}
```

```java
public class Test extends ActionSupport {
	private User user;
	
	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

	public String execute() {
		return "success";
	}
}
```

```html
<s:property value="user.username"/>
```

**结果：**在值栈中user的值为空，即对象user没有初始化。   

**解决方式：**  
1.传入参数user.username=tom后，Struts2才会new这个对象。    
2.在action中直接new一个对象，即`private User user = new User();`  

注：当User类没有空参构造，而又传入参数user.username=tom时，user依然是空值。  
因为User类没有空参构造方法，并没有成功构造一个user对象。
{: .notice}  

```java
//错误方式
public class User {
	private String username = "tom";
	
//	public User() {
//		System.out.println("user constructor");
//	}
	public User(String username) {
		super();
		this.username = username;
	}
	
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
}
```

结论：想初始化domain model，可以自己new也可以传参数值，但需要有空的构造方法。
{: .notice}  


### 访问值栈中对象的普通方法和属性  

```html
<s:property value="user.username.length()"/>
```

*** 


### 访问静态方法和属性  

访问静态方法：@类名@方法名  
访问静态方法：@类名@属性名

需要在struts.xml中设置才可以访问静态方法
<constant name="struts.ognl.allowStaticMethodAccess" value="true"></constant>


```java
public class Test{
	public static String STR = "STATIC STRING";
	
	public static String out() {
		return "Static Method";
	}
}
```

```html
<s:property value="@cn.xsw.test.Test@out()"/><br/>
 <s:property value="@cn.xsw.test.Test@STR"/><br/>
```


***


### 访问普通类的构造方法  

```java
public class User {
	private String username;
	
	public User(String username) {
		super();
		this.username = username;
	}
	
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

```html
<s:property value="new cn.xsw.test.User('tom')"/>  

result:User [username=tom]
```

***  

### 访问集合  

**List**  

```java
package cn.xsw.test;

public class User {
	private String username;

	public User(String username) {
		super();
		this.username = username;
	}

	@Override
	public String toString() {
		return "User [username=" + username + "]";
	}
	
}
```


```java
public class Test{
	private List<User> users = new ArrayList<User>();
	
	public Test() {
		users.add(new User("a"));
		users.add(new User("b"));
		users.add(new User("c"));
	}
	
	public List<User> getUsers() {
		return users;
	}

	public void setUsers(List<User> users) {
		this.users = users;
	}

	public String execute() {
		return "success";
	}
}
```

```html
1.访问List：<s:property value="users"/>
result:[User [username=a], User [username=b], User [username=c]]

2.访问List中某个元素：<s:property value="users[0]"/>
result:User [username=a]

3.访问List中元素某个属性的集合：<s:property value="users.{username}"/>
result:[a, b, c](User类一定要有get方法，否则无法访问)

4.访问List中元素某个集合的特定值<s:property value="users[0].username"/>|
<s:property value="users.{username}[0]"/>
result:[a]
```


**Set** 

```java
public class User {
	private String username;
	
	public User(String username) {
		super();
		this.username = username;
	}
	
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

```java
public class Test{
	private Set<User> users = new HashSet<User>();
	
	public Test() {
		users.add(new User("a"));
		users.add(new User("b"));
		users.add(new User("c"));
	}
	
	public Set<User> getUsers() {
		return users;
	}

	public void setUsers(Set<User> users) {
		this.users = users;
	}

	public String execute() {
		return "success";
	}
}
```


```html
1.访问Set:<s:property value="users"/>
result:[User [username=a], User [username=c], User [username=b]]

2.访问Set中某个元素:<s:property value="users[0]"/>
result:set无序，取不到该元素
```


**Map**  

```java
public class User {
	private String username;
	
	public User(String username) {
		super();
		this.username = username;
	}
	
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

```java
public class Test{
	private Map<String, User> userMap = new HashMap<String, User>();
	
	public Test() {
		userMap.put("a", new User("a"));
		userMap.put("b", new User("b"));
		userMap.put("c", new User("c"));
	}
	public Map<String, User> getUserMap() {
		return userMap;
	}

	public void setUserMap(Map<String, User> userMap) {
		this.userMap = userMap;
	}

	public String execute() {
		return "success";
	}
}
```

```html
1.访问Map:<s:property value="userMap"/>
result:{b=User [username=b], c=User [username=c], a=User [username=a]}

2.访问Map中某个元素:<s:property value="userMap.a"/> | 
<s:property value="userMap['a']"/> | <s:property value="userMap[\"a\"]"/>
result:User [username=a]

3.访问Map中所有的key:<s:property value="userMap.keys"/>
result:[b, c, a]

4.访问Map中所有的value:<s:property value="userMap.values"/>
result:[User [username=b], User [username=c], User [username=a]]

5.<li>访问容器的大小：<s:property value="userMap.size()"/> | <s:property value="userMap.size"/>
result:3
```


***


### 访问投影(过滤器)


this指循环过程中的当前对象  
?# 过滤条件  
^# 开头  
$# 结尾
{: .notice}  
 
```html
投影：<s:property value="users.{?#this.age==1}[0]"/> 
投影：<s:property value="users.{^#this.age>1}.{age}"/>
投影：<s:property value="users.{$#this.age>1}.{age}"/>
投影：<s:property value="users.{$#this.age>1}.{age} == null"/>
```


***

### []  

```html
[]:<s:property value="[0]"/> 在Value Stack中从第0个开始一直访问到栈底
[]:<s:property value="[1]"/> 在Value Stack中从第1个开始一直访问到栈底
```






