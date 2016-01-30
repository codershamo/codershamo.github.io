---
layout: post
title: Spring源码解析2-HierarchicalBeanFactory
---

## HierachicalBeanFactory

### 功能介绍

继承BeanFactory, 继承HierachiclaBeanFactory的bean factories可以成为层次结构的一部分。<br>

### HierachicalBeanFactory方法

* **BeanFactory getParentBeanFactory():**<br>
返回父bean factory,如果没有，返回null.

* **boolean containsLocalBean(String name):**<br>
判断本地Bean factory是否包含给定名称的bean,忽略祖先上下文中定义的beans。<br>
这个方法是BeanFactory中的containsBean方法的替代方法，忽略了祖先中定义的beans。