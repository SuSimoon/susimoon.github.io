---
layout: post
title:  "SpringMVC-整合MyBaits"
date:   2017-03-20
excerpt: "本篇介绍了SpringMVC与MyBaits的整合"
tag:
- SpringMVC
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">导入Jar包</a>  
><a href="#2">创建配置文件</a>  
><a href="#3">创建包结构</a>   
><a href="#4">前端控制器</a> 
><a href="#3">程序示例</a>   

***

<a name="1"></a>

## <center>导入Jar包</center> 

MyBaits的jar包  
MyBaits和Spring整合包  
Spring的所有jar包(包括SpringMVC的包)  
数据库驱动包  
log4j日志
{: .notice} 

***

<a name="2"></a>

## <center>创建配置文件</center> 

**Spring：**
applicationContext-dao.xml---配置数据源、SqlSessionFactory、mapper扫描器  
applicationContext-service.xml---配置service接口  
applicationContext-transaction.xml--事务管理  
**SpringMVC：**
springmvc.xml---springmvc的配置，配置处理器映射器、适配器、视图解析器  
**MyBaits：**
SqlMapConfig.xml---MyBaits的配置文件，配置别名、settings、mapper  
{: .notice} 


### applicationContext-dao.xml：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd 
		http://www.springframework.org/schema/mvc 
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd 
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-3.2.xsd 
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
		http://www.springframework.org/schema/tx 
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd ">
	
	<!-- 加载配置文件 -->
	<context:property-placeholder location="classpath:db.properties" />
	<!-- 数据库连接池 -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="10" />
		<property name="maxIdle" value="5" />
	</bean>

	<!-- SqlsessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource"/>
		<!-- mybatis配置文件 -->
		<property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml"/>
	</bean>
	
	<!--  扫描器自动扫描mapper，生成代理对象 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="cn.xsw.ssm.mapper"/>
		<!-- 使用sqlSessionFactoryBeanName -->
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
	</bean>	

</beans>
```

### applicationContext-transaction.xml：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd 
		http://www.springframework.org/schema/mvc 
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd 
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-3.2.xsd 
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
		http://www.springframework.org/schema/tx 
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd ">


	 <!-- 使用声明式事务配置，可以有效规范代码 -->
	 
	 <!-- 事务管理器 -->
	 <bean id="transactionManager" 
	 		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	 	<property name="dataSource" ref="dataSource"/>
	 </bean>
	 
	 <!-- 通知 -->
	 <tx:advice id="txAdvice" transaction-manager="transactionManager">
	 	<tx:attributes>
	 		<tx:method name="save*" propagation="REQUIRED"/>
	 		<tx:method name="insert*" propagation="REQUIRED"/>
	 		<tx:method name="update*" propagation="REQUIRED"/>
	 		<tx:method name="delete*" propagation="REQUIRED"/>
	 		<tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
	 		<tx:method name="select*" propagation="SUPPORTS" read-only="true"/>
	 		<tx:method name="get*" propagation="SUPPORTS" read-only="true"/>
	 	</tx:attributes>
	 </tx:advice>
	 
	 <!-- aop -->
	 <aop:config>
	 	<aop:advisor advice-ref="txAdvice"
	 				 pointcut="execution(* cn.itcast.ssm.service.impl.*.*(..))"/>
	 </aop:config>
	
</beans>
```

### springmvc.xml：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd 
		http://www.springframework.org/schema/mvc 
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd 
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-3.2.xsd 
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
		http://www.springframework.org/schema/tx 
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd ">

		<!-- 使用spring组件扫描Controller -->
		<context:component-scan base-package="cn.xsw.ssm.controller" />
		
		<!-- 通过annotation-driven可以替代下边的处理器映射器和适配器 -->
		<!-- <mvc:annotation-driven conversion-service="conversionService"/> -->

		<!-- 注解处理器映射器 -->
		<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
	
		<!-- 注解适配器 -->
		<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
			
		<!-- 视图解析器: -->
		<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
			<property name="prefix" value="/WEB-INF/jsp/" />
			<property name="suffix" value=".jsp" />
		</bean>
</beans>
```

### SqlMapConfig.xml:

```xml
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 别名 -->
	<typeAliases>
		<package name="cn.xsw.ssm.pojo"/>
	</typeAliases>

	<!-- 
		由于使用spring和mybatis整合的mapper扫描器，不需要再配置mapper扫描器
		<mappers>
			<package name="cn.xsw.ssm.mapper" />
		</mappers>
	 -->	
</configuration>
```

***

<a name="3"></a>

## <center>创建包结构</center> 

cn.xsw.ssm.po  
cn.xsw.ssm.mapper  
cn.xsw.ssm.controller  
cn.xsw.ssm.service
{: .notice} 

***

<a name="4"></a>

## <center>前端控制器</center> 

在web.xml中：

```xml
<!-- 前端控制器 -->
<servlet>
	<servlet-name>springmvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<!-- 加载springmvc配置 -->
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/springmvc.xml</param-value>
	</init-param>
</servlet>

<servlet-mapping>
	<servlet-name>springmvc</servlet-name>
	<url-pattern>*.action</url-pattern>
</servlet-mapping>
```

```xml
<!-- 配置spring容器监听器 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>/WEB-INF/classes/spring/applicationContext-*.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```


***

<a name="5"></a>

## <center>程序示例</center> 

需求：根据条件查询商品信息，返回商品列表。  
items等单表可通过逆向工程生成。  

### mapper：

包装类：

```java
//商品包装类
public class ItemsQueryVo {
	private ItemsCustom itemsCustom;

	//get、set...
}
```

**mapper.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.xsw.ssm.mapper.ItemsMapperCustom">
	
	<!-- sql片段 -->
	<sql id="query_items_where">
		<if test="itemsCustom!=null">
			<if test="itemsCustom.name!=null and itemsCustom.name!=''" >
				and name like '%${itemsCustom.name}%'
			</if>
			<if test="itemsCustom.id!=null" >
				and id = #{itemsCustom.id}
			</if>
		</if>	
	</sql>
	
	<select id="findItemsList" parameterType="cn.xsw.ssm.po.ItemsQueryVo"
			resultType="cn.xsw.ssm.po.ItemsCustom">
		select * from items 
		<where>
			<include refid="query_items_where"/>
		</where>
	</select>
</mapper>
```

**mapper.java:**

```java
public interface ItemsMapperCustom {
	public List<ItemsCustom> findItemsList(ItemsQueryVo itemsQueryVo) 
			throws Exception;
}
```

### service：

```java
public interface ItemsService {
	public List<ItemsCustom> findItemsList(ItemsQueryVo itemsQueryVo) 
			throws Exception;
}
```

```java
public class ItemsServiceImpl implements ItemsService {
	//注入mapper
	@Autowired
	private ItemsMapperCustom itemsMapperCustom;
	
	@Override
	public List<ItemsCustom> findItemsList(ItemsQueryVo itemsQueryVo)
			throws Exception {
		
		return itemsMapperCustom.findItemsList(itemsQueryVo);
	}
}
```

### controller：

```java
@Controller
public class ItemsController {
	@Autowired
	private ItemsService itemsService;
	@RequestMapping("/queryItems")
	public ModelAndView queryItems() throws Exception {
		//调用service查询商品列表
		List<ItemsCustom> itemsList = itemsService.findItemsList(null);
		ModelAndView modelAndView = new ModelAndView();
		modelAndView.addObject("itemsList",itemsList);
		modelAndView.setViewName("itemsList");
		return modelAndView;
	}
}
```

在applicationContext-dao.xml中不需要配置，因为已经设置了自动扫描器扫描mapper。  


