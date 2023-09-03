---

title: "Spring AOP - Pointcut"
date: 2015-06-10 11:19:44 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---

What is `Pointcut` of Spring AOP? let's describe it. A `Pointcut` defines what `Advice`s are required at what `Join Point`s. In fact all business logic methods of the class are not required all services, that means each business logic method might require different service even some methods don't require. So Pointcut can informs IOC container that what business methods of a class needs what type of services.    
<!-- more -->
There are two types of AOP:

- StaticMethodMatcherPointcut
- DynamicMethodMatcherPointcut

Pointcut verifys whether a particular method of particular class is eligible for getting the advice or not. Spring AOP has two static pointcut class.

- NameMatchMethodPointcut
- RegexpMethodPointcutAdvisor

In above two static pointcut class, they given by spring AOP framework. And they only verify whether the method name is matching with given condition or not, but they doesn't check whether the class names are matching or not. If you want to verify the class name, you can implement `StaticMethodMatcherPointcut` abstract class and override the `getClassFilter()`, `matches()` methods.

The dynamic pointcut is almost same with static pointcut, the only difference is that the dynamic pointcut can verify the arguments of the method at runtime, but static pointcut couldn't.