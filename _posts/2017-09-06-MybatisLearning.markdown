---
layout: post
title:  "Mybatis Learning"
date:   2017-09-06 13:25:35 +0200
categories: OpenSource JAVA
no-post-nav: true
---

MyBatis源码学习笔记

## Mybatis介绍

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。
    
## 基本使用

### 1. 初始化

		String resource = "org/mybatis/example/mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

#### a. 加载资源文件

通过ClassLoader获取资源的流, 遍历所有的classLoader   
  
		classLoader //传入的classLoader
        defaultClassLoader //未实例化的classLoader
        Thread.currentThread().getContextClassLoader() //线程上下文classLoader
        getClass().getClassLoader() //当前类加载的classLoader
        systemClassLoader		//系统classLoader

通过classLoader获取资源

		classLoader.getResourceAsStream(resource)

####b. 解析配置文件
	
- 解析properties  

- 解析typeAliases
- 解析objectFactory
- 解析objectWrapperFactory
- 解析settings
- 解析databaseIdProvider
- 解析typeHandlers
- 解析mappers
	
### 2.

## 