---

title: "Spring AOP - Join Point"
date: 2015-06-09 17:19:23 +0800
comments: true
categories: Backend
tags: [Java, Spring]
---

In Spring AOP, while we create business logic of the method we usually need to inject the services at different places or points. So we call these places or points as `JoinPoint`.   

At a join point place services will be injected at run time. And while executing business logic of method, the services can be injected in following 3 join points.

- Before business logic of method starts
- After business logic of method complete
- If business logic of method throws exception at run time.   

In a `join point`, many `Aspect`s(just name, not implementation) can be injected. Actually the `Advice`(the implementation of Aspect) is injected.