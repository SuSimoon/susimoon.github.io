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
`<constant name="struts.ognl.allowStaticMethodAccess" value="true"></constant>`


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

**1.List**  

```java
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


**2.Set** 

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


**3.Map**  

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


*** 

## <center>valueStack</center>

### valueStack的概念

valueStack是值栈。

1.从技术角度：valueStack就是一个接口（com.opensymphony.xwork2.util.ValueStack）  
2.从使用角度：valueStack就是一个容器
  
1.ValueStack有一个实现类叫OgnlValueStack。  
2.每一个action都有一个ValueStack。  
(一个请求，一个request，一个action，一个valueStack，valueStack生命周期就是request生命周期)    
3.valueStack中存储了当前action对象以及其它常用web对象。(request、session、application、parameters)  
4.Struts2框架将valueStack以“struts.valueStack”为名存储到request域中。
{: .notice}

ongl与valueStack的关系：  
valueStack主要是将action数据携带到页面上，在页面上通过ognl表达式将valueStack中数据获取出来。
{: .notice}


### valueStack结构

ValueStack中存在root属性 (CompoundRoot)和context属性（OgnlContext）  
　　• CompoundRoot 就是 ArrayList  
　　• OgnlContext  就是 Map  

list集合中存储的是action相关信息。从list中获取数据，可以不使用#号。    
map集合中存储的是相关映射信息，包含paramters,request,session,application,attr等。从map中获取数据，需要使用#。(其实在struts2中的map--context其实就是ognlContext)
{: .notice}


结论:  
	1.ValueStack它有两部分List和Map。  
	2.在Struts2中List就是root，Map就是ognlContext。  			
	3.默认情况下，在Struts2中从valueStack获取数据就是从root中获取（即不加#）。  
	4.在Struts2中valueStack内部有两部分Map(ognlContext)和List(root)，而在map中持有root。
{: .notice}


![](http://ww4.sinaimg.cn/large/83e1667djw1f8ys7xu9wwj21hs0q0tcj.jpg)


### ValueStack和ActionContext的关系

值栈对象valueStack是请求时创建的。  
创建ActionContext对象过程中，创建值栈对象valueStack。  
在ActionContext中持有了valueStack的引用。  

```java
ActionContext ctx = ActionContext.getContext();
if (ctx != null) {
    stack = ctx.getValueStack();
}
```


### 获取值栈对象  

对于valueStack获取有两种方式:  

1.通过request获取    

```java
ValueStack vs=(ValueStack) ServletActionContext.getRequest().getAttribute(ServletActionContext.STRUTS_VALUESTACK_KEY);
```

2.通过ActionContext获取(推荐)  

```java		
ValueStack vs=ActionContext.getContext().getValueStack();
```


### 向值栈保存数据(主要针对root)


主要有两个方法：  

1.push(Object obj) --> 底层就是root.add(0,obj)，将数据存储到栈顶。  
2.set(String name, Object obj); --> 底层是将数据封装到HashMap中，再将这个HashMap通过push存储。
{: .notice}	


### 在JSP中获取值栈的数据 

root中数据不需要#，而context中数据需要#。  

• 获取root中的数据：  

```html
1.如果栈顶是一个Map集合，获取时，可以直接通过Map集合的key来获取value。  
	`<s:property  value=""/>`  		
2.如果栈顶数据不是一个Map，没有key值，可以使用序号来获取。  
	`<s:property value="[0]">`       从0的位置向下查找所有  
	`<s:property value="[0].top">`   只查找0位置上的数据
```
	
	
• 获取OgnlContext中数据:  

```java
1.request数据      request.setAttribute(); 
2.session数据      session.setAttribute();
3.application数据  application.setAttribute();
4.attr   		  依次从request、session、application中查找
5.parameters 	  获取请求参数
```

注：Struts2包装后的数据，不是真正的数据。
{: .notice}


```java
public class OgnlDemo2Action extends ActionSupport {
	@Override
	public String execute() throws Exception {
		// 向valueStack中存储数据(root)
		ValueStack vs = ActionContext.getContext().getValueStack();
		
		vs.set("username", "tom");
		vs.push("hello");
		return SUCCESS;
	}
}
```

```html
<body>
	获取OgnlContext中数据<br>
	<%
		request.setAttribute("rname", "rvalue");
		session.setAttribute("sname", "svalue");
		application.setAttribute("aname", "avalue");
	%>
	
	<s:property value="#request.rname"/><br>
	<s:property value="#session.sname"/><br>
	<s:property value="#application.aname"/><br>
	
	<s:property value="#attr.sname"/><br>
	<s:property value="#parameters.username[0]"/><!--地址栏输入参数?username=tom&username=ann-->
</body>
```

***

## <center>valueStack的应用</center>  

ValueStack主流应用:就是解决将action数据携带到jsp页面

### action向jsp携带的数据类型

	
**1.信息（String类型数据）**  

1.fieldError   针对某一个字段错误信息(常用于表单校验)  
2.actionError  逻辑操作时普通错误信息，不针对某一个字段(例如登录失败)  
3.message 	   通用消息
{: .notice}

```java
this.addFieldError("msg", "字段错误信息");
this.addActionError("Action全局错误信息");
this.addActionMessage("Action的消息信息");
```

在jsp中使用Struts2提供标签显示消息信息:  

```html
<s:fielderror fieldName="msg"/>
<s:actionerror/>
<s:actionmessage/>
```


**2.数据（复杂类型数据）**

• 在action中存储数据:

```java
List<User> users = new ArrayList<User>();
users.add(new User("a"));
users.add(new User("b"));
users.add(new User("c"));

ValueStack vs = ActionContext.getContext().getValueStack();
vs.push(users);
//vs.set("users", users);
```

• 在页面上获取数据:  

```html
1.使用push存储数据，可用[0].top获取：
<s:property value="[0].top.users" />

2.使用set存储数据，直接使用key获取：
<s:property value="users" />

3.取出集合中每个元素时，使用iterator：

将集合中迭代出来每一个元素起个名称叫user，var内容是存储在context中，需要使用#。
<s:iterator value="[0].top" var="user">
	username:<s:property value="#user.username"/>
</s:iterator>

没有给迭代出的元素起名，则不需要使用#。
<s:iterator value="[0].top">
	username:<s:property value="username"/>
</s:iterator>
```


### 默认压入到valueStack中的数据


**1.访问的action对象会被压入到valueStack中**（类似属性驱动）
	
(源码)DefaultActionInvocation的init方法中有stack.push(action);	
Action如果想传递数据给JSP，只有将数据保存到成员变量，并且提供get方法就可以了。
(获取时直接使用get后面的名字) 
	

**2.ModelDriveInterceptor会执行下面的操作**（类似模型驱动）

将实现了ModelDrive接口的action中getModel方法的返回值即model对象压入到了valueStack。	

```java
//(源码)
ModelDriven modelDriven = (ModelDriven) action;
ValueStack stack = invocation.getStack();
Object model = modelDriven.getModel();
if (model !=  null) {
	stack.push(model);
}
```

**压入到valueStack中的model分析：**

```java
public class User {
	private String username;
	
	public User(String username) {
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
public class Test extends ActionSupport implements ModelDriven<User>{
	private User user = new User("tom");
	@Override
	public User getModel() {
		return user;
	}
	public String execute() {
		return "success";
	}
}
```

![](http://wx2.sinaimg.cn/large/83e1667dgy1fceng821xzj20ty0lsgos.jpg)

1.Struts2中所有拦截器执行前会先把访问的action对象压入到valueStack中。(图中cn.xsw.test.Test)      
2.ModelDriveInterceptor使getModel中的model压入到valueStack中。(图中栈顶cn.xsw.test.User)
{: .notice}  

当在execute方法中又新建一个User对象时：  

```java
public class Test extends ActionSupport implements ModelDriven<User>{
	private User user = new User("tom");
	@Override
	public User getModel() {
		return user;
	}
	public String execute() {
		user = new User("John");
		return "success";
	}
}
```

![](http://wx2.sinaimg.cn/large/83e1667dgy1fceng9fio2j20u80lktbx.jpg)

1.action先入栈。此时user指向的是tom。  
2.ModelDriven拦截器会将getModel方法返回值对象压栈，也就是将返回的user压入栈中，此时指向的是tom。
3.当action的execute方法执行时，对user重新赋值，跳到成功页面，此时user指向了John，但modelDriven中的指向不变。  
4.debug方法又调用了getModel方法，返回execute中的的user，因为execute方法已经执行了，user重新初始化。返回的是John。  
5.总结：action中的user会变，modelDriven中的user不会变。
{: .notice}


***

## <center>valueStack相关的结构信息总结</center>

![](http://ww2.sinaimg.cn/large/83e1667djw1f8ztg8g2pvj21la0tm45q.jpg)

注：图中context指向的是ognlContext整体。
{: .notice}


***

## <center>EL表达式可以访问valueStack中数据</center>

Struts2框架中所使用的request对象，是增强后的request对象。  
${username} --> request.getAttribute("username");  
增强后的request，会首先在request域范围查找；如果查找不到，会在valueStack中查找request对象。
{: .notice}

StrutsPreparedAndExecuteFilter的doFilter代码中：request = prepare.wrapRequest(request);  
1.对Request对象进行了包装，StrutsRequestWrapper   
2.重写request的getAttribute：   

```java
Object attribute = super.getAttribute(s);
	if (attribute == null) {
	   attribute = stack.findValue(s);
}
```


```html
<body>
	1.OGNL获取:<s:property value="username"/><br>
	2.EL获取:${username}	
</body>
```








