---
layout: post
title: Spring源码解析-BeanFactory
---

#BeanFactory
##简介
BeanFactory是访问spring容器的根接口，它是一个bean容器最基本的客户端视图。
高级接口如ListableBeanFactory和ConfigrablueBeanFactory用来实现特定目标。
BeanFactory接口由持有bean definitions的对象实现，每个bean definition由唯一的spring name标识。
根据bean definition，factory将一个独立的被容器管理的对象（prototype design pattern)或是一个共享的单例（singleton design pattern)。