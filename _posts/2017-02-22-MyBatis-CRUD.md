---
layout: post
title:  "MyBatis-CRUD"
date:   2017-02-22
excerpt: "本篇介绍了MyBatis的增删改查操作"
tag:
- Java 
- MyBatis
- CRUD
- SSM框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">R:用户查询</a>  
><a href="#2">C:用户添加</a>   
><a href="#2">U:用户修改</a>  
><a href="#3">D:用户删除</a>   


***

<a name="1"></a>

## <center>用户查询</center>   

### 根据id查询：  

```xml
<mapper namespace="test">
	<select id="findUserById" parameterType="int" resultType="cn.xsw.mybatis.User">
		select * from user where id= #{id}
	</select>
</mapper>
```

```java
User user = null;
user = sqlSession.selectOne("test.findUserById", 2);
```

`#{}`：表示一个占位符，向占位符输入参数时，Mybaits自动进行java类型和jdbc类型的转换。  
程序员不需要考虑参数的类型。比如：传入的是字符串，Mybaits最终将拼接好的参数两边加上单引号。


### 根据名称模糊查询：

```xml
<mapper namespace="test">
<select id="findUserByName" parameterType="string" resultType="cn.xsw.mybatis.User">
	select * from user where name like '%${value}%'
</select>
</mapper>
```


```java
List<User> list = null;
list = sqlSession.selectList("test.findUserByName", "a");
System.out.println(Arrays.toString(list.toArray()));
```

`${}`：表示sql的拼接，接受参数时将参数内容不加任何修饰的拼接在sql中。  
缺点：不能防止sql注入。 


注：使用`select * from user where name like '#{value}'`的方式也可以进行模糊查询，   
但是需要用户输入参数时加上%，这是不可取的。
{: .notice}

***

<a name="2"></a>

## <center>用户添加</center> 


```xml
<!-- 添加用户
	parameterType：输入参数的类型，User对象包括name,address
	#{}接收pojo数据，可以使用OGNL解析出pojo的属性值
	#{name}表示从parameterType中获取pojo的属性值
-->
<select id="insertUser" parameterType="cn.xsw.mybatis.User">
	insert into user(name, address) values(#{name},#{address}) 
</select>
```

```java
User user = new User();
user.setName("kate");
user.setAddress("Beijing");

sqlSession.insert("test.insertUser", user);
sqlSession.commit(); //需要提交事务才能添加
```


### 主键返回 - 自增形式的主键

需求：user对象插入到数据库后，新记录的主键要通过user对象返回，通过user获取主键值。  
解决思路：通过`LAST_INSERT_ID()`获取刚插入记录的自增主键值，在insert语句后执行，就可以获取自增主键。  

```xml
<!-- 主键返回
selectKey：用于进行主键返回，定义了获取主键值的sql
order：设置selectKey中sql执行的顺序，相对于insert语句来说
keyProperty：将主键值设置到哪个属性
resultType：select LAST_INSERT_ID()的结果类型
-->
<insert id="insertUser" parameterType="cn.xsw.mybatis.User">
	<selectKey order="AFTER" keyProperty="id" resultType="int" >
		select LAST_INSERT_ID()
	</selectKey>
	insert into user(name, address) values(#{name},#{address}) 
</insert>
```

```java
System.out.println(user.getId()); //不配置则返回的是0
```


### 主键返回 - uuid形式的主键


需求：使用mysql的uuid机制生成主键的好处是不考虑数据库移植后主键冲突问题。
实现思路：先查询uuid得到主键，将主键设置到user对象中，再将user对象插入数据库。

```xml
<!-- mysql的uuid生成主键 -->
<insert id="insertUser" parameterType="cn.xsw.mybatis.User">
	<selectKey keyProperty="id" order="BEFORE" resultType="string">
		select uuid()
	</selectKey>
	insert into user(uuid, name, address) values(#{uuid},#{name},#{address})
</insert>
```

注：此时需要插入uuid。  
{: .notice}


***

<a name="3"></a>

## <center>用户修改</center> 


```xml
<update id="updateUser" parameterType="cn.xsw.mybatis.User">
	update user set name=#{name}, address=#{address} where id=#{id}
</update>
```

```java
User user = new User();
user.setId(6);
user.setName("Ann");
user.setAddress("Nanjing");
sqlSession.update("test.updateUser", user);
```


***

<a name="4"></a>

## <center>用户删除</center> 

```xml
<delete id="deleteUser" parameterType="int">
	delete from user where id=#{id}
</delete>
```

```java
sqlSession.delete("test.deleteUser", 7);
```


注：对于sqlSession的CRUD操作，传入Parameter时，若是单个数据，则传入该数据；若是多个数据，则直接传入对象。
{: .notice}

