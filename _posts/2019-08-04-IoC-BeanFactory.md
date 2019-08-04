---
layout: post
title:  "IoC - BeanFactory"
date:   2019-08-04
excerpt: "本篇介绍了Spring提供的两种容器类型"
tag:

- Spring
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---

***

## <center>BeanFactory体系结构</center>


![](http://wx3.sinaimg.cn/mw690/83e1667dly1g5nhrqk2tpj211a0d841s.jpg)

**BeanFactory：**BeanFactory只是一个接口，我们最终需要一个该接口的实现来进行Bean的管理。DefaultListableBeanFactory就是这么一个比较通用的BeanFactory实现类。

**DefaultListableBeanFactory：**比较通用的BeanFactory实现类。除了间接地实现了BeanFactory接口，还实现了BeanDefinitionRegistry接口，

**BeanDefinitionRegistry：**该接口定义抽象了Bean的注册逻辑，是在BeanFactory的实现中担当Bean注册管理的角色。

基本上，BeanFactory接口只定义**如何访问容器内管理的Bean**的方法，
各个BeanFactory的具体实现类负责具体Bean的注册以及管理工作。 

**BeanDefinition：**每一个受管的对象，在容器中都会有一个BeanDefinition的实例(instance)与之相对应，该 BeanDefinition的实例负责保存对象的所有必要信息，包括其对应的对象的class类型、是否是抽象类、构造方法参数以及其他属性等。

当客户端向BeanFactory请求相应对象的时候，BeanFactory会通过这些信息为客户端返回一个完备可用的对象实例。

RootBeanDefinition和ChildBeanDefinition是BeanDefinition的两个主要实现类。



## <center>BeanFactory的对象注册与依赖绑定</center>



