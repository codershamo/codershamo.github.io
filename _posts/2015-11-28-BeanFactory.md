---
layout: post
title: Spring源码解析1-BeanFactory
---

##BeanFactory

###BeanFactory功能介绍

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

### BeanFactory方法
BeanFactory的所有方法：
![beanfactory](/public/img/beanfactory.png)
下面逐一进行讲解：

* **String FACTORY_BEAN_PREFIX = "&"**:<br>
用于间接引用一个FactoryBean实例并将它与它所生成的beans区分开来。例如，假如一个名为myJndiObject的bean是一个FactoryBean,
&myJndiObject将返回这个factory，而不是这个factory返回的实例。

* **getBean()**:<br>
返回一个共享或独立的实例，根据具体的bean。<br>
这个方法允许一个spring BeanFactory替代单例或原型模式。对于单例的beans，调用者可以保持对它们的引用。<br>
将别名翻译回相应的标准bean名称。如果这个bean没有在当前工厂实例中找到，则会访问其父工厂。<br>
也可以根据类型返回相应的bean实例，或者是名称与类型同时满足的bean实例。<br>

* **Object getBean(String name, Object... args)**:<br>
这个方法允许用具体的构造方法参数和工厂方法参数来覆盖bean中默认的参数。<br>
args： 当创建一个具体的bean时所指定的参数（只有当创建一个新的实例而不是获得已有的实例时使用）。

* **boolean containsBean(String name)**:<br>
判断当前的factory是否包含给定名称的bean定义或是由外部注册的单例实例。如果给定名称是一个别名，此方法将其他转化回其对应的标准bean名称。
如果当前factory是（分层）hierachical的，如果当前factory中找不到，则会到其父factory中寻找。如果一个bean定义或单例实例被找到，
无论这个bean定义是具体的还是抽象的，延迟或马上的（lazy or eager)的，是否在scope中的，这个方法都会返回true。

* **boolean isSingleton(String name)**:<br>
判断一个bean是否是一个共享的单例。
*注意：* 这个方法返回false并不一定表明是当前bean是独立的实例，而只表明它是非单例的实例，它也可能是一个作用域内（scoped）的实例。
请使用isPrototype()方法来判断独立实例。

* **boolean isPrototype(String name)**:<br>
与isSingleton类似，*注意*同样。

* **boolean isTypeMatch(String name, ResolvableType typeToMatch)**:<br>
检查bean的名称与类型是否匹配。即判断getBean方法根据name返回的实例类型与typeToMatch是否匹配。

* **Class<?> getType(String name)**:<br>
确定给定名称的bean的类型。即判断getBean方法根据name返回的实例的类型。<br>
对于一个FactoryBean，返回这个FactoryBean创建的对象类型，与FactoryBean的getObjectType()返回的类型相同。

* **String[] getAliases(String name)**:<br>
返回指定名称的所有别名。所有的这些别名在getBean调用时都指向同一个名称。如果name是一个别名，那它对应的原始bean name和其他别名都将被返回，
而此别名为数组的第一个元素。

### BeanFactory的继承接口
继承BeanFactory的接口如下：
![BeanFactory sub](/public/img/beanfactory sub.png)

