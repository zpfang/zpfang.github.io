---
layout: post
title:  "Mybatis Learning"
date:   2017-09-06 13:25:35 +0200
categories: OpenSource JAVA
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
可以动态替换的属性配置  
如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载,并存与config对象中的variables属性变量中：  
	- 在 properties 元素体内指定的属性首先被读取。
	- 然后根据properties 元素中的resource属性读取类路径下属性文件或根据url属性指定的路径读取属性文件(优先读取resource属性)，并覆盖已读取的同名属性。
	- 最后读取作为方法参数传递的属性，并覆盖已读取的同名属性。
- 解析typeAliases  
通过config获取typeAliasRegistry, typeAliasRegistry在构造函数中注册了基本类型的别名
	- 如果配置的别名是包, 则扫描包下所有的类并注册到typeAliasRegistry中的map中, 
	- 如果配置的是类则直接注册(如果未配置别名则获取类的简单名称,首字母小写),
- 解析plugins  
通过interceptor属性配置的类名或者别名, 通过config中的typeAliasRegistry获取类的(先查找别名map,如果不存在则尝试直接通过类名获取类型),并强转为Interceptor类型  
然后通过Interceptor实例调用setProperties将配置的属性设置到插件类中  
最后调用config的addInterceptor将插件类加入到interceptorChain(维护一个拦截器列表)实例中
- 解析objectFactory(用来创建所有的结果返回对象)  
同上plugins, 获取配置的类名或者别名,并获取改名称的类型, 然后创建该类型的实例, 并调用setProperties设置改实例的属性, 最后设置到config的objectFactory变量中
- 解析objectWrapperFactory
同上objectFactory, 获取配置的类名或者别名,并获取改名称的类型, 然后创建该类型的实例, 但没有setProperties方法来设置属性, 最后设置到config的objectWrapperFactory(默认没有包装类)变量中
- 解析settings
设置mybtis运行时行为, 这里不就一一列举, 将属性一一设置到config的属性
- 解析environments  
id和事务和数据源共同组成config中的environment变量     
配置多个环境, 但一个SqlSessionFactory只能选择一个环境(有几个数据源必须有几个SqlSessionFactory实例) 
事务管理器支持JDBC(jdbc的提交和回滚)和MANAGED(无提交和回滚)两种, 通常由spring来提供事务管理器     
数据源支持UNPOOLED(一次查询一次连接), POOLED(连接池), JNDI(统一连接池)三种方式
- 解析databaseIdProvider
获取当前是什么数据类型,是通过数据源获取MetaData来判断数据库类型的, sql中通过databaseId来针对不同的数据库执行不同的语句
- 解析typeHandlers
同typeAliases解析, 如果配置了包,则扫描包下实现了TypeHandler的类, 配置了类则解析JavaType,JdbcType,TypeHandler实现类, 最后注册到config的变量typeHandlerRegistry(默认注册了基本的typeHandler)中  
- 解析mappers
	
### 2.

## 