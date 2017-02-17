---
layout: post
title:  "Spring-集成JUnit测试"
date:   2017-02-17
excerpt: "本篇介绍了Spring如何集成JUnit测试"
tag:
- Java 
- Spring
- JUnit
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  
  
   
***


## <center>Spring集成JUnit测试</center> 

**1.程序中要有JUnit环境**

**2.导入spring与JUnit整合jar包:**
`spring-test-3.2.0.RELEASE.jar`

**3.测试代码:**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class SpringTest {
	@Autowired
	private User user;
	
	@Test
	public void demo(){
		//不需要写加载的文件了
		System.out.println(user);
	}
}
```

```java
//以往的方式：
public class SpringTest {
	@Test
	public void demo() {
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
		User user = (User) applicationContext.getBean("user");
		System.out.println(user);
	}
}
```