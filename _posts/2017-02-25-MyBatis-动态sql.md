---
layout: post
title:  "MyBatis-动态sql"
date:   2017-02-24
excerpt: "本篇介绍了MyBatis中的"
tag:
- Java 
- MyBatis
- 动态sql
- SSM框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">原始dao开发方式</a>  
><a href="#2">创建mapper代理的方式</a>   
><a href="#3">mapper接口返回单个对象和集合对象</a>    


***

<a name="1"></a>

## <center>sqlMpaConfig.xml配置</center> 

```xml
<!--select * from user WHERE name like '%a%' and address = ? -->
<select id="findUserList" parameterType="userQueryVo" resultType="user">
	select * from user
	<!-- where标签相当 于where关键字，可以自动去除第一个and -->
	<where>
		<if test="userCustomer!=null">
			<if test="userCustomer.name!=null and userCustomer.name!=''">
			and name like '%${userCustomer.name}%'
			</if>
			<if test="userCustomer.address!=null and userCustomer.address!=''">
			and address = #{userCustomer.address}
			</if>
		</if>
	</where>
</select>
```


```java
public void test() {
	SqlSession sqlSession = sqlSessionFactory.openSession();
	// 创建代理对象
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
	// 构造查询条件
	UserQueryVo userQueryVo = new UserQueryVo();
	UserCustomer userCustomer = new UserCustomer();
	userCustomer.setName("a");
	userCustomer.setAddress("Beijing");
	userQueryVo.setUserCustomer(userCustomer);
	
	List<User> list = userMapper.findUserList(userQueryVo);
	sqlSession.close();
	System.out.println(Arrays.toString(list.toArray()));
}
```


***


### <center>sql片段</center> 


通过sql片段可以将通用的sql语句抽取出来，单独定义，在其它的statement中可以引用sql片段。
常用于where条件、查询列。  

```xml
<!-- 注意：不要将where标签放在sql片段 -->
<sql id="query_user_where">
	<if test="userCustomer!=null">
		<if test="userCustomer.name!=null and userCustomer.name!=''">
			and name like '%${userCustomer.name}%'
		</if>
		<if test="userCustomer.address!=null and userCustomer.address!=''">
			and address = #{userCustomer.address}
		</if>
	</if>
</sql>
```


```xml
<select id="findUserList" parameterType="userQueryVo" resultType="user">
	select * from user
	<where>
		<!-- 引用sql片段，如果sql片段和引用处不在同一个mapper必须前边加namespace -->
		<include refid="query_user_where"></include>
	</where>
</select>
```

***


### <center>foreach</center> 

在statement中通过foreach可遍历parameterType中的集合类型。  

需求：根据多个用户id查询用户信息。

**1.在userQueryvo中定义`list<Integer> ids`存储多个id：**

```java
//用户id集合
private List<Integer> ids;
```


**2.使用foreach遍历list：**

```xml
<sql id="query_user_where">
	<!-- 最终拼接的效果：select * from user where id in (1,3,5)
		collection：集合的属性
		open：开始循环拼接的串
		close：结束循环拼接的串
		item：每次循环取到的对象
		separator：每两次循环中间拼接的串
	 -->
	<foreach collection="ids" open="in (" close=")" item="id" separator=",">
		#{id}
	</foreach>

	<!-- select * from user where (id=1 or id=3 or id=5)
	  <foreach collection="ids" open="(" close=")" item="id" separator="or">
	 	id = #{id}
	 </foreach>
	-->
</sql>
```

**3.测试：**

```java
public void test() {
	SqlSession sqlSession = sqlSessionFactory.openSession();
	// 创建代理对象
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
	// 构造查询条件
	UserQueryVo userQueryVo = new UserQueryVo();
	List<Integer> ids = new ArrayList<Integer>();
	ids.add(1);
	ids.add(3);
	ids.add(5);
	userQueryVo.setIds(ids);
	
	List<User> list = userMapper.findUserList(userQueryVo);
	sqlSession.close();
	System.out.println(Arrays.toString(list.toArray()));
}
```

