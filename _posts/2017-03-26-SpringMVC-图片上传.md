---
layout: post
title:  "SpringMVC-图片上传"
date:   2017-03-26
excerpt: "本篇介绍了SpringMVC中用@ ModelAttribute来进行数据回显"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  
    
     

***

<a name="1"></a>

### 图片存储目录设置 

把图片上传到工程目录不利于进行工程维护。  
一般使用专门的图片服务器(http，比如apache、tomcat)。  
下例使用图片虚拟目录，通过虚拟目录访问硬盘上存储的图片目录 。  
{: .notice} 

双击Servers里的Tomcat：

![](http://wx4.sinaimg.cn/large/83e1667dly1fe0d9iqn00j20ty0a3ac9.jpg)

### 配置图片上传解析器

SpringMVC使用commons-fileupload进行图片上传。

1.导入commons-fileupload的jar包：  

commons-fileupload-1.2.2.jar  
commons-io-2.4.jar  
{: .notice} 

2.在springmvc.xml中配置commons-fileupload对应的SpringMVC的图片上传解析器：

```xml
<!-- 文件上传 -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<!-- 设置上传文件的最大尺寸为5MB -->
	<property name="maxUploadSize">
		<value>5242880</value>
	</property>
</bean>
```

### 在jsp页面中：

```html
<form id="itemForm" action="..." method="post" enctype="multipart/form-data">
	<tr>
		<td>商品图片</td>
		<td>
			<c:if test="${itemsCustom.pic !=null}">
				<img src="/pic/${itemsCustom.pic}" width=100 height=100/>
				<br/>
			</c:if>
			<input type="file"  name="pictureFile"/> 
		</td>
	</tr>
</form>
```


### 在Controller中：

```java
@RequestMapping("/editItemSubmit")
public String editItemSubmit(Model model,Integer id,
		@ModelAttribute(value="itemsCustom") ItemsCustom itemsCustom,
		//上传图片
		MultipartFile pictureFile
		)throws Exception{
	
	//进行图片上传
	if(pictureFile!=null){
		//图片上传成功后，将图片的地址写到数据库
		String filePath = "F:\\develop\\upload\\temp\\";
		//上传文件原始名称
		String originalFilename = pictureFile.getOriginalFilename();
		//新的图片名称
		String newFileName = UUID.randomUUID() +originalFilename.substring(originalFilename.lastIndexOf("."));
		//新文件
		File file = new java.io.File(filePath+newFileName);
		//将内存中的文件写入磁盘
		pictureFile.transferTo(file);
		//图片上传成功，将新图片地址写入数据库
		itemsCustom.setPic(newFileName);
	}
}
```
