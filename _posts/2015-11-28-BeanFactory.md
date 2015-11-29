---
layout: post
title: Spring源码解析-BeanFactory
---

##BeanFactory

###简介

BeanFactory是访问spring容器的根接口，它是一个bean容器最基本的客户端视图。
高级接口如ListableBeanFactory和ConfigrablueBeanFactory用来实现特定目标。

BeanFactory接口由持有bean definitions的对象实现，每个bean definition由唯一的spring name标识。
根据bean definition，factory将返回一个独立的被容器管理的对象（prototype design pattern)或是一个共享的单例（singleton design pattern)。
返回哪个对象由bean factory的配置决定：所有的API是相同的。spring2.0以后，根据具体的应用上下文有更多的scope可用。

BeanFactory是应用组件的注册中心，并且集中化了应用组件的配置（例如，一个独立的对象不再需要单独去读属性文件）。

*注意*：通过setter和constructors的信赖注入的方式配置（push配置）应用对象一般情况下好于使用任何pull配置的方式，比如BeanFactory lookup。
spring的信赖注入的功能是使用这个接口及其子接口实现的。

一般地，BeanFactory将载入存储在配置源(如xml文档)里的bean definitions信息，并使用org.springframework.beans包来配置beans。然而，
也可以从java代码中直接返回java对象。definitions的存储形式并没有限制：LDAP, RDBMS, XML等。实现应该支持beans之间的相互引用。

与ListableBeanFactory不同的是，HirachicalBeanFactory接口的所有操作都会检查其parent factory。如果这个工厂实例没有被找到，那它的最近的父工厂将被
访问。这个工厂实例中的所有bean都应该覆盖其任何父工厂同名的bean。

Bean facotry的实现应该尽可能支持标准bean的生命周期。所有的初始化方法及其他标准顺序为：<br>
1. BeanNameAware's {@code setBeanName}<br>
2. BeanClassLoaderAware's {@code setBeanClassLoader}<br>
3. BeanFactoryAware's {@code setBeanFactory}<br>
4. ResourceLoaderAware's {@code setResourceLoader}
   (only applicable when running in an application context)<br>
5. ApplicationEventPublisherAware's {@code setApplicationEventPublisher}
   (only applicable when running in an application context)<br>
6. MessageSourceAware's {@code setMessageSource}
   (only applicable when running in an application context)<br>
7. ApplicationContextAware's {@code setApplicationContext}
   (only applicable when running in an application context)<br>
8. ServletContextAware's {@code setServletContext}
   (only applicable when running in a web application context)<br>
9. {@code postProcessBeforeInitialization} methods of BeanPostProcessors<br>
10. InitializingBean's {@code afterPropertiesSet}<br>
11. a custom init-method definition<br>
12. {@code postProcessAfterInitialization} methods of BeanPostProcessors

当关闭一个bean factory时，运行下面的生命周期方法：<br>
1. DisposableBean's {@code destroy}<br>
2. a custom destroy-method definition

![beanfactory.png](http://codershamo.github.io/public/img/beanfactory.png)
![beanfactory.png](/public/img/beanfactory.png)