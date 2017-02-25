---
layout: post
title:  "MyBatis-mapper"
date:   2017-02-24
excerpt: "本篇介绍了MyBatis中的mapper接口"
tag:
- Java 
- MyBatis
- mapper
- SSM框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">原始dao开发方式</a>  
><a href="#2">创建mapper代理的方式</a>   
><a href="#3">mapper接口返回单个对象和集合对象</a>    


***

<a name="1"></a>

## <center>原始dao开发方式</center>  

程序员需要写dao接口和dao的实现类。  

### 程序示例：

**接口：**

```java
public interface UserDao {
	public User findUserById(int id);
}
```

**实现类：**

```java
public class UserDaoImpl implements UserDao {
	private SqlSessionFactory sqlSessionFactory;
	// 将SqlSessionFactory注入
	public UserDaoImpl(SqlSessionFactory sqlSessionFactory) {
		this.sqlSessionFactory = sqlSessionFactory;
	}
	@Override
	public User findUserById(int id) {
		//创建SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
		// 根据id查询用户信息
		User user = sqlSession.selectOne("test.findUserById", id);
		sqlSession.close();
		return user;
	}
}
```


**测试类：**

```java
public class UserDaoImplTest {
	private SqlSessionFactory sqlSessionFactory;
	@Before
	public void setUp() throws IOException {
	// 配置文件SqlMapConfig.xml
		String resource = "SqlMapConfig.xml"; 
		// 加载配置文件到输入流
		InputStream inputStream = Resources.getResourceAsStream(resource);
		// 创建会话工厂
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}
	@Test
	public void testFindUserById() {
		UserDao userDao = new UserDaoImpl(sqlSessionFactory);
		User user = userDao.findUserById(2);
		System.out.println(user);
	}
}
```

### 原始dao开发方式的问题：

1.dao的实现类中存在重复代码，整个mybatis操作的过程代码模板重复（先创建sqlsession、调用sqlsession的方法、关闭sqlsession）  
2.dao的实现类中存在硬编码，调用sqlsession方法时将statement的id硬编码。  


***

<a name="2"></a>

## <center>创建mapper代理的方式</center>  

程序员只需要写dao接口，dao接口实现对象由mybatis自动生成代理对象。

### 1.创建mapper接口：

mybatis提出了mapper接口，相当于dao接口。

mapper接口的命名方式建议：表名Mapper(UserMapper)

```java
public interface UserMapper {	
	public User findUserById(int id);				
}
```

1.mapper.xml中statement的id就是mapper.java中方法名。  
2.mapper.xml中statement的parameterType和mapper.java中方法输入参数类型一致。  
3.mapper.xml中statement的resultType和mapper.java中方法返回值类型一致。
{: .notice}

![](http://wx1.sinaimg.cn/large/83e1667dgy1fd1c7e5j52j212u0axq4c.jpg)

### 2.mapper.xml中namespace指定为mapper接口的全限定名：

通过mapper.xml和mapper.java进行关联(通过xml找到接口)。   

```xml
<mapper namespace="cn.xsw.mybatis.mapper.UserMapper">
```

### 3.将mapper.xml在SqlMapConfig.xml中加载

```xml
<!-- 通过resource引用mapper的映射文件 -->
<mapper resource="mapper/UserMapper.xml" />
```

### 4.测试类：

```java
public class UserMapperTest {
	private SqlSessionFactory sqlSessionFactory;
	@Before
	public void init() throws IOException {
		String resource = "SqlMapConfig.xml"; 
		InputStream inputStream = Resources.getResourceAsStream(resource);
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}
	@Test
	public void test() {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		//创建代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		User user = userMapper.findUserById(1);
		System.out.println(user);
	}

}
```


***

<a name="3"></a>

## <center>mapper接口返回单个对象和集合对象</center>  

不管查询记录是单条还是多条，在statement中resultType定义一致，都是单条记录映射的pojo类型。  
mapper接口方法返回值如果是单个对象，生成的代理对象内部通过selectOne获取记录；  
mapper接口方法返回值如果是集合对象，生成的代理对象内部通过selectList获取记录。 
{: .notice} 

```java
public interface UserMapper {	
	public User findUserById(int id);	
	public List <User> findUserByName(String name);
}
```

```java
List<User> list = userMapper.findUserByName("a");
System.out.println(Arrays.toString(list.toArray()));
```

```xml
<!-- 自定义查询：
	parameterType：指定包装类型
	userCustomer是userQueryVo中的属性，可通过ognl来获取属性值。
 -->
<select id="findUserList" parameterType="userQueryVo" resultType="user">
	select * from user where name like '%${userCustomer.name}%'
</select>
```

