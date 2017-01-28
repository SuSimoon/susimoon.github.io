 Struts2的路径问题是根据action的路径而不是jsp路径来确定的，所以尽量不要用相对路径。  
解决办法是统一使用绝对路径：在jsp中用request.getContextRoot方式来拿到webapp的路径。  
或者使用ME中常用的指定basePath

<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";

%>

<base href="<%basePath%>"/>