---
title: The difference of set cookie in Tomcat and Jetty
date: 2025-04-25 17:42:11
categories: Backend
tags: [Java, Spring, Spring Boot, Tomcat, Jetty, Cookie]
---

In previous blog [Switching Tomcat with Jetty in Spring Boot Easily](https://stonefishy.github.io/2025/04/11/switching-tomcat-with-jetty-in-spring-boot-easily/), to resolve the spring boot 2 tomcat security issue in short time, we switching `Tomcat` with `Jetty` in Spring Boot. We found there is a difference by set cookie handling between `Tomcat` and `Jetty` by default. Let's first talk about the cookie.

## What is cookie?
A cookie is a small piece of data that is stored on the user's computer by the web server. It is used to store user preferences, login information, and other information that the user wants to be remembered. The cookie is sent to the user's browser when the user makes a request to the web server. The browser then sends it back to the web server with subsequent requests.

## Cookie Attributes
There are several attributes that can be set for a cookie:

- `Domain`: The domain attribute specifies the domain for which the cookie is valid. If the domain is not specified, the cookie is valid for the entire domain.
- `Path`: The path attribute specifies the path on the server where the cookie is valid. If the path is not specified, the cookie is valid for the entire path.
- `Max-Age`: The max-age attribute specifies the maximum age of the cookie in seconds. If the max-age is not specified, the cookie is valid until the browser is closed.
- `Expires`: The expires attribute specifies the expiration date of the cookie. If the expires attribute is not specified, the cookie is valid until the browser is closed.
- `Secure`: The secure attribute specifies that the cookie should only be sent over HTTPS connections.
- `HttpOnly`: The httpOnly attribute specifies that the cookie should not be accessible to client-side scripts.
- `SameSite`: The sameSite attribute specifies whether the cookie should be sent with cross-site requests. The sameSite attribute can have the following values: `Strict`, `Lax`, or `None`.

When `Max-Age` and `Expires` are not specified, the cookie is valid until the browser is closed. If `Max-Age` is specified, the cookie is valid for the specified number of seconds. If `Expires` is specified, the cookie is valid until the specified date. If both `Max-Age` and `Expires` are specified, the cookie is valid for the minimum of the two values.

## Set-Cookie in Spring Boot
In Spring Boot, the `Set-Cookie` header is used to send cookies to the user's browser. The `Set-Cookie` header is sent in the response to the user's request. The `Set-Cookie` header contains the name, value, and other attributes of the cookie. Here is an example of the `Set-Cookie` header in Spring Boot:

```
Set-Cookie: JSESSIONID=1234567890; Path=/; HttpOnly; Secure
```
In this example, the `JSESSIONID` is the name of the cookie, `1234567890` is the value of the cookie, `Path` is the path on the server where the cookie is valid, `/` is the path, `HttpOnly` is a flag that indicates that the cookie should not be accessible to client-side scripts, `Secure` is a flag that indicates that the cookie should only be sent over HTTPS connections, and `SameSite` is not set.

In `Network` tab of `Chrome DevTools`, we can see the `Set-Cookie` header in the response headers. That means these cookie are set by web server and sent to the user's browser and stored in the user's computer.

{% image /assets/images/java/set-cookie-in-response-headers.png, alt="Set-Cookie in response headers" %}

And we can also see the cookies in `Application` tab of `Chrome DevTools`. The cookies are stored in the user's browser and can be accessed by the web server in subsequent requests.

{% image /assets/images/java/cookie-in-browser-application-tab.png, alt="Cookies in Browser Application tab" %}

## Difference of Set-Cookie in Tomcat and Jetty
Okay, let's talk about the difference of `Set-Cookie` header handling between `Tomcat` and `Jetty`. Here we will use `curl` command to send a request to the server and see the `Set-Cookie` header in the response for default configuration.

### Tomcat
If we want to reduce `XSS cross-site scripting attacks` and `prevent reading and modifying cookie contents`, we can set the `HttpOnly` attribute for the cookie. In `Tomcat 6`,  we need to config it in the `tomcat/conf/context.xml` file. Add the `useHttpOnly="true"` attribute in `<Context>` tag.

```xml
<Context useHttpOnly="true">
</Context>
```

But for `Tomcat 7` and later version. We don't need to config it in `context.xml` any more. The `useHttpOnly` attribute is set to `true` by default. It is introduced in `Tomcat 7` official doc (https://tomcat.apache.org/tomcat-7.0-doc/config/context.html)

{% image /assets/images/java/http-only-in-tomcat7.png, alt="HttpOnly in Tomcat 7" %}

Below is the screenshot that using `curl` command to send a request to our current Spring Boot application with `Tomcat` server:

{% image /assets/images/java/set-cookie-with-http-only.png, alt="Set-Cookie with HttpOnly" %}

From the screenshot, you can see the `Set-Cookie` header in the response. The `HttpOnly` attribute is appended in the cookie by default.

BTW, from `Spring Boot` 2 version, it is starting use `Tomcat` 8 version which will set `HttpOnly` attribute by default in the cookie.

### Jetty
For `Jetty`, the `HttpOnly` attribute is `not` set by default. We need to config it in the `web.xml` file. Add the `<http-only>` element in `<cookie-config>` tag.

```xml
<session-config>
    <cookie-config>
        <http-only/>
    </cookie-config>
</session-config>
```

We also can set the `HttpOnly` attribute for the cookie in the code.

``` java
SessionCookieConfig.setHttpOnly(true);
```

We can find the evidence in the `Jetty` official doc https://jetty.org/docs/jetty/12/programming-guide/server/session.html.

{% image /assets/images/java/http-only-in-jetty.png, alt="HttpOnly in Jetty" %}

By default, the `HttpOnly` attribute is not set in the `Set-Cookie` header in jetty. Below is the screenshot that using `curl` command to send a request to our current Spring Boot application with `Jetty` server by default configuration:

{% image /assets/images/java/set-cookie-without-http-only.png, alt="Set-Cookie without HttpOnly" %}

As you can see, the `HttpOnly` attribute is not set in the `Set-Cookie` header in `Jetty` by default when we use the `curl` command. The code are same, just simply switch `Tomcat` to `Jetty` in the `pom.xml` file in previous blog.

## Summary
The `Set-Cookie` header is used to send cookies to the user's browser. The `HttpOnly` attribute is used to prevent client-side scripts from accessing the cookie. The `Secure` attribute is used to ensure that the cookie is only sent over HTTPS connections. The `SameSite` attribute is used to prevent cross-site requests. The difference of `Set-Cookie` header handling between `Tomcat` and `Jetty` is that `Tomcat` set the `HttpOnly` attribute by default, while `Jetty` does not set it by default. We need to config it in the `web.xml` file for `Jetty` or set it in the code for `Tomcat`. So when you switche `Tomcat` to `Jetty`, you need to check the `HttpOnly` attribute in the `Set-Cookie` header for session id for security reason.
