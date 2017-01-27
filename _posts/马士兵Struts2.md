1.具体的Action实现可以是一个普通的java类，里面有public String execute方法即可
2.或者实现Action接口（该接口中只有一个execute方法）
3.最常用的是继承ActionSupport，好处是可以直接使用Struts2封装的方法  

Struts2的路径问题是根据action的路径而不是jsp路径来确定的，所以尽量不要用相对路径。  
解决办法是统一使用绝对路径：在jsp中用request.getContextRoot方式来拿到webapp的路径。  
或者使用ME中常用的指定basePath

<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";

%>

<base href="<%basePath%>"/>