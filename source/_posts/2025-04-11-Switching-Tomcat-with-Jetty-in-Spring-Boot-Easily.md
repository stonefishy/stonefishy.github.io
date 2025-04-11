---
title: Switching Tomcat with Jetty in Spring Boot Easily
date: 2025-04-11 11:16:30
categories: Backend
tags: [Java, Spring, Spring Boot, Tomcat, Jetty]
---

## Background 
Recently, we received the critical security vulnerability which is CVE-2025-24813 (https://www.cvedetails.com/cve/CVE-2025-24813/) information from cloud security team. It request the team to check if there any projects are using tomcat which affected by this vulnerability, and remediate it quickly.

The CVE-2025-24813 describe below, and publised at 2025-03-10 17:15:35. It's new found vulnerability.

> Path Equivalence: 'file.Name' (Internal Dot) leading to Remote Code Execution and/or Information disclosure and/or malicious content added to uploaded files via write enabled Default Servlet in Apache Tomcat.

Our all microservices are using tomcat as servlet container. And we're using `spring-boot-starter-tomcat` version 2.6.4 which is affacted. Actually all spring boot 2 contains this vulnerability. Even in most of embedded tomcat libraries in sprint boot 3.

{% image /assets/images/java/spring-boot-starter-tomcat-2.6.4.png, alt="CVE-2025-24813 in Spring Boot Starter Tomcat 2.6.4" %}

This tomcat vulnerability has been fixed since `spring-boot-starter-tomcat` version `3.4.3`.

{% image /assets/images/java/spring-boot-starter-tomcat-3.4.3.png, alt="CVE-2025-24813 Resolved in Spring Boot Starter Tomcat 3.4.3" %}

## Solution

To resolve this vulnerability issue, there are two solutions: 
1. One is upgrade `spring-boot-starter-tomcat` to latest version. 
2. We can switch to other servlet container, such as `Jetty`.

For the first solution, we have to upgrade our entire all microservice from Spring Boot 2 to Spring Boot 3 which requires Java 17. Official guide https://spring.io/blog/2022/05/24/preparing-for-spring-boot-3-0. Currently our all microserces are still using Java 8. In short term, It's hard to upgrade Java 8 to Java 17 and Spring Boot 2 to Spring Boot 3, it will take much time and efforts. There also third party libraries requires to be upgrade when upgrade Java 8 to Java 17.

More over, we are using `spring-cloud-starter-netflix-zuul`, in spring cloud github, it mentioned `spring-cloud-starter-netflix-zuul` not work with `spring-boot 3.0` and spring 6.0, it has been out of support. It suggests to using `Spring Cloud Gateway`.  Refer official issue link: https://github.com/spring-cloud/spring-cloud-netflix/issues/4158. Besides currently the Spring Cloud Gateway does not support the SSO SAML. Hope it supports SAML in future.

So the solution 2 is best to resolve this vulnuerability in short term.

## Jetty

{% image /assets/images/java/jetty-logo.png, alt="Jetty" %}

`Jetty` is a part of the Eclipse foundation and started in 1995. Jetty is not just a web server and servlet container. But it offers support for WebSocket, HTTP/2, OSGi, JNDI, JMX, JAAS, and many other integrations. Additionally  Jetty is popular among developers and also works as an embedded tool for a lot of frameworks, cloud services, application servers, and devices. 

According to stats Jetty maintains a market share of 10% among Java application servers. Some popular companies that use Jetty are `Google`, `Yahoo`, `Nuxeo` and `Canva`. 

Jetty can:

- Jetty is user friendly and has a better interface than Tomcat.
- Can be used as a part of or full Java application server stack.
- Might be applied as a part of other frameworks due to its flexibility.
- Supports cloud style operations.
- The conceptual weight of the framework is less, very fast, and thin.
- Better for handling simultaneous users compared to Tomcat.
- Small memory trace to work speedily.
- Users can use it easily because the required knowledge and skills are very few.

Compare with `Tomcat`, they have the following simlarities:

- Both these tools are open source platforms and written in Java.
- Companies use them in production environments (live releases).
- They can process all components of the Core Java enterprise.
- Both tools have large community support.

## Code Implementation
To switching Tomcat to Jetty in Spring Boot is very easily. You don't need to update any logic code, the one thing you need to do is that exclude the embedded Tomcat `spring-boot-starter-tomcat` from `spring-boot-stater-web` and add dependency `spring-boot-starter-jetty` in `Maven` or `Gradle`

### For Maven Users:
Update the `pom.xml` and making below changes.

``` xml
<dependencies>
    <!-- Exclude Tomcat starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <!-- Add Jetty starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
</dependencies>
```

### For Gradle Users:
Update the `build.gradle` file as below:

``` groovy
dependencies {
    // Exclude Tomcat starter
    implementation('org.springframework.boot:spring-boot-starter-web') {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-tomcat'
    }

    // Add Jetty starter
    implementation 'org.springframework.boot:spring-boot-starter-jetty'
}
```

After you make above changes. Spring Boot stop auto-configuring `Tomcat` as the embedded server and to set up `Jetty` instead. {% pbg success, It’s a seamless transition that requires no additional code. %} Spring Boot’s auto-configuration capabilities recognize the absence of Tomcat and the presence of Jetty and automatically configure your application to use Jetty as its embedded servlet container.


## Testing
It's always a good idea to fine-tune your server settings to ensure optimal performance. With Jetty, you can easily configure thread pools, port settings, and more through the `application.properties` or `application.yml` file in your Spring Boot project.

For example, to change the server port and configure the max thread pool size in application.properties, you can add below settings in `application.properties` file.

```
server.port=8001
server.jetty.threads.max=200
```

Once you have complete configuration. You can start your application. The Jetty servlet container started like below.

{% image /assets/images/java/spring-boot-jetty-started.png, alt="Spring Boot Application Started with Jetty" %}

## Summery
Switching from `Tomcat` to `Jetty` in a Spring Boot application isn’t just feasible; it’s straightforward with Spring Boot’s flexible architecture. Whether you’re looking for increased performance, reduced resource consumption, or simply exploring alternatives, transitioning to Jetty offers tangible benefits with minimal hassle.







