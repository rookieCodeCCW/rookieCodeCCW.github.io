---
title: "复习spring(一)"
layout: post
category: note
tags: [review]
excerpt: "spring问题总结"
---
# spring是什么？
 * 轻量级的IOC和框架 基础框架。常见的配置方式有三种 xml 配置文件 注解 基于java的配置。
   * spring core 
   * spring context
   * spring AOP
   * spring DAO
   * spring ORM
   * spring Mvc
   
# spring三大特点 
 * 控制反转 IOC
 * 依赖注入 DI
 * 面向切面编程 AOP
 
# BeanFactory和ApplicationContext有什么区别？
 * BeanFactory可以理解为bean集合的工厂类 实例化bean 可以控制bean生命周期 initialization methods/destruction methods
 * Application Context如同bean factory一样具有bean定义、bean关联关系的设置，根据请求分发bean的功能 但是还有其他三个功能是BeanFactory不具备的
   * 提供了支持国际化的文本消息。
   * 统一的资源文件读取方式
   * 可读取上下文
   
# spring常用注解
 * 声明的注解
   * @Component  组件
   * @Service 业务层
   * @Repository  DAO层
   * @Controller 展现层和控制器层
 * 注入bean的注解
   * @Autowired spring提供
   * @Inject 
   * @Resource 
 * java配置类注解
   * @Configuration  声明配置类 相当于spring中的xml
   * @Bean 声明当前的方法返回的是一个bean 代替xml中的bean
   
# Spring基于xml注入bean的几种方式
 * set方法注入
 * 构造器注入 ①通过index设置参数的位置；②通过type设置参数类型
 * 静态工厂注入
 * 实例工厂
 
# Spring 框架中都用到了哪些设计模式？
 * 工厂模式 BeanFactory就是工厂模式 用来创建对象的实例
 * 单例模式 bean就是单例模式
 * 代理模式 pring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术
 * 模板方法 用来解决代码重复的问题。比如. RestTemplate, JmsTemplate, JpaTemplate
 * 观察者模式 定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring中listener的实现--ApplicationListener
 
# Spring事务的实现方式和实现原理
