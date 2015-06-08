---
layout: post
title: "Spring AOP - Around Advice"
date: 2015-06-08 09:42:08 +0800
comments: true
categories: [Java, Spring, AOP]
---

In this blog, let us talk about `Around Advice`. There are some few points about it.   

- `Around Advice` is combination of `Before Advice` and `After Advice`.
- In a single `Around Advice` we can implement both *before* and *after* services.
- **Note**, `Around Advice` is not given by *spring framework*, it is from *Open Source* implementation called `AOP` alliance.
- `Around Advice` can be used by any framework which supports `AOP`.    
- `Around Advice` can access the return value of business method and it can modify the value and it can return a different value back to the client, as return type is Object, but in the `After Advice` its not possible right, as its return type is void.
<!-- more -->

In order to create `Around Advice`, we should implement an interface called `MethodInterceptor` and override the method called `invoke` which has a prarameter `MethodInvocation` type. We can use this parameter to invoke our business logic method like ``` methodInvoation.proceed() ```. And we can add our before services before this code execution and put after services after this code.   

Let's see an instance.

``` java 
package spring.test.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class MyAroundAdvice implements MethodInterceptor{

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		// Adding before service in here.
		
		invocation.proceed();
		
		// Adding after service in here.
		// we can change the return value we want via below return codes.
		
		return null;
	}

}
```

In above codes, we can see that we put the before services before the `proceed()` method and put the after services after it. and we can change the return value of the logic method.   

So now let us to see a full expample.

``` java MyLogicInterface.java
package spring.test.aop;

public interface MyLogicInterface {
	int add(int a, int b);
}

```

``` java MyLogicClass.java
package spring.test.aop;

public class MyLogicClass implements MyLogicInterface {

	@Override
	public int add(int a, int b) {
		System.out.println(String.format("The internal result of %s + %s is: %s", a, b, a + b));
		return a + b;
	}

}
```
In our logic class, we print the result value before the return of method.

``` java MyAroundAdvice.java
package spring.test.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class MyAroundAdvice implements MethodInterceptor {

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		// Adding before service
		methodDescription(invocation);

		Object currentResult = invocation.proceed();

		// Adding after service.
		Object changedResult = changedReturnValue(invocation);

		return changedResult == null ? currentResult : changedResult;
	}

	private void methodDescription(MethodInvocation invocation) {
		String methodName = invocation.getMethod().getName();
		Object[] args = invocation.getArguments();

		String description = String.format("Before Services : %s(%s, %s)",
				methodName, args[0], args[1]);
		System.out.println(description);
	}

	private Object changedReturnValue(MethodInvocation invocation) {
		Object[] args = invocation.getArguments();
		int secondParam = (int) args[1];

		// changed the return value to 0 when the second parameter is less than 0.
		if (secondParam < 0)
			return 0;

		return null;
	}

}
```
In this our around advice. we print the method description in our before service and changed the return value to 0 when the second parameter is less than 0 in after service.   

``` xml springConfig.xml
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN"
			"http://www.springframework.org/dtd/spring-beans-2.0.dtd">

<beans>
	<bean id="myAroundAdvice" class="spring.test.aop.MyAroundAdvice"/>
	
	<bean id="myLogicClass" class="spring.test.aop.MyLogicClass"/>
	
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="proxyInterfaces" value="spring.test.aop.MyLogicInterface"/>
		<property name="interceptorNames">
			<list>
				<value>myAroundAdvice</value>
			</list>
		</property>
		<property name="target" ref="myLogicClass"/>
	</bean>
</beans>
```
The spring config xml is same with before except the interceptorNames.

``` java App.java
package spring.test.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

	private static ApplicationContext applicationContext;

	public static void main(String[] args) {
		applicationContext = new ClassPathXmlApplicationContext("springConfig.xml");
		MyLogicInterface logicInterface = (MyLogicInterface)applicationContext.getBean("proxyFactoryBean");
		
		int result1 = logicInterface.add(1, 10);
		System.out.println(String.format("After Advice - The result is: %s \n", result1));
		
		int result2 = logicInterface.add(1, -10);
		System.out.println(String.format("After Advice - The result is: %s", result2));
	}

}
```

You will notice that we pass -10 to second parameter in second times method called. Let's see the result:    

	Before Services : add(1, 10)
	The internal result of 1 + 10 is: 11
	After Advice - The result is: 11 

	Before Services : add(1, -10)
	The internal result of 1 + -10 is: -9
	After Advice - The result is: 0

You see, the internal result is -9, but after service, the result is changed to 0. So the return value is changed by our `Around Advice`.

***Note again:** The `Around Advice` can change the return value of business logic method, but `After Advice` cannot.*