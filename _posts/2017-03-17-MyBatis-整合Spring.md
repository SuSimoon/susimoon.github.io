---
layout: post
title:  "MyBatis-整合Spring"
date:   2017-02-29
excerpt: "本篇介绍了MyBatis与Spring的整合"
tag:
- MyBatis
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">整合开发原始dao接口</a>  
><a href="#2">整合开发mapper代理方法</a>     

***

<a name="1"></a>

## <center>整合开发原始dao接口</center> 

### 配置SqlSessionFactory：

```xml
<!-- SqlsessionFactory -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<!-- 数据源 -->
	<property name="dataSource" ref="dataSource"/>
	<!-- MyBatis配置文件 -->
	<property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml"/>
</bean>
```


### 开发并配置Dao：


```java
public interface UserDao {
	//根据id查询用户信息
	public User findUserById(int id) throws Exception;
}
```


```java
//继承SqlSessionDaoSupport
public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {
	@Override
	public User findUserById(int id) throws Exception {
		// 创建SqlSession
		SqlSession sqlSession = this.getSqlSession();
		// 根据id查询用户信息
		User user = sqlSession.selectOne("test.findUserById", id);
		return user;
	}
}
```


### 测试Dao接口：

```java
public class UserDaoImplTest {
	private ApplicationContext applicationContext;
	@Before
	public void setUp() throws Exception {
		//创建spring容器
		applicationContext = new ClassPathXmlApplicationContext("spring/applicationContext.xml");
	}

	@Test
	public void testFindUserById() throws Exception {
		//从spring容器中获取UserDao这个bean
		UserDao userDao = (UserDao) applicationContext.getBean("userDao");
		User user = userDao.findUserById(1);
		System.out.println(user);	
	}
}
```



***

<a name="2"></a>

## <center>整合开发mapper代理方法</center> 

### 1.使用MapperFactoryBean：

使用此方法对于每个mapper都需要配置，比较繁琐。  

```xml
<!-- 配置mapper：
	MapperFactoryBean：用于生成mapper代理对象
	 -->
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
	<property name="mapperInterface" value="cn.xsw.mybatis.mapper.UserMapper"/>
	<property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

### 2.MapperScannerConfigurer(扫描mapper)：

使用扫描器自动扫描mapper，生成代理对象，比较方便。

```xml
<!-- 
	MapperScannerConfigurer：mapper的扫描器，将包下边的mapper接口自动创建代理对象，
	自动创建到spring容器中，bean的id是mapper的类名（首字母小写）
-->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- 配置扫描包的路径:
		如果要扫描多个包，中间使用半角逗号分隔 -->
		<property name="basePackage" value="cn.xsw.mybatis.mapper"/>
		<!-- 使用sqlSessionFactoryBeanName
		而不是sqlSessionFactory(否则会有冲突) -->
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>	
	</bean>
```


