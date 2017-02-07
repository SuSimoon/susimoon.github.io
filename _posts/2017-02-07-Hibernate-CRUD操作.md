---
layout: post
title:  "Hibernate-CRUD操作"
date:   2017-02-06
excerpt: "本篇介绍了Hibernate"
tag:
- Java 
- Hibernate
- CRUD
- SSH框架
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---   

><a href="#1">Hibernate概述</a>    
><a href="#2">SessionFactory</a>  
><a href="#3">Session</a>
><a href="#4">Transaction</a>
><a href="#4">Query</a>
><a href="#4">Criteria</a>

***

SQL 查询的是表和表中的字段，不区分大小写

HQL Hibernate Query Language
	与SQL相似，查询的是对象和对象中的属性。关键字不区分大小写，但类名与属性名区分。

HQL符合数据库的操作形式，而Criteria符合面向对象方式