---
title: Understanding the X-Frame-Options HTTP Header
date: 2024-06-14 14:36:28
categories: Frontend
tags: [Web]
---

Recently, we build a frontend website as a nginx docker image, before go live on production. We asking the security team to do the security scan for the website on stage environment. One of security issues indicates the X-Frame-Options HTTP header is not set properly. It will cause the website to be vulnerable to clickjacking attacks.

## Clickjacking Attack
Clickjacking is a type of security vulnerability that allows an attacker to trick a user into clicking on a link or button on a malicious website that is designed to look like the legitimate website. This can happen when the attacker embeds the malicious website within a frame on the legitimate website, which can trick the user into clicking on the malicious link or button.

To prevent clickjacking attacks, web developers can use the X-Frame-Options HTTP header to specify whether a web page can be displayed within a frame or iframe. This header can have three possible values: DENY, SAMEORIGIN, and ALLOW-FROM uri.

## What is X-Frame-Options?
The X-Frame-Options is an HTTP response header used to control whether a web page can be displayed within a frame or iframe. It helps to mitigate clickjacking attacks by preventing malicious websites from embedding a vulnerable site within a frame and tricking users into taking unintended actions.

## How it works
The X-Frame-Options header can have three possible values: DENY, SAMEORIGIN, and ALLOW-FROM uri.

`DENY`: This value prevents the page from being displayed in a frame, regardless of the site attempting to do so.

`SAMEORIGIN`: With this value, the page can be displayed in a frame on the same origin as the page itself. This restricts the frame to the same origin as the parent page.

`ALLOW-FROM uri`: Here, the page can only be displayed in a frame on the specified origin.

## Implementation
To implement the X-Frame-Options header, simply include the header in the server's HTTP response. It can be implemented on code programming, server configuration, or web server configuration.

### Code Programming
Below is an example of how to set the header using different programming languages:

#### Using Node.js (Express)

``` javascript
// Set X-Frame-Options header to DENY
app.use((req, res, next) => {
  res.setHeader('X-Frame-Options', 'DENY');
  next();
});
```

#### Using Django (Python)

``` python
# Set X-Frame-Options header to SAMEORIGIN
response['X-Frame-Options'] = 'SAMEORIGIN'
```

#### Using ASP.NET (C#)

``` C#
// Set X-Frame-Options header to ALLOW-FROM
Response.AddHeader("X-Frame-Options", "ALLOW-FROM https://example.com");
```

### Server Configuration
#### Nginx
To configure Nginx to send the X-Frame-Options header, add this either to your http, server or location configuration:

``` 
add_header X-Frame-Options SAMEORIGIN always;
```

#### Apache
To configure Apache to send the X-Frame-Options header for all pages, add this to your site's configuration:

```
Header always set X-Frame-Options "DENY"
```

#### IIS
To configure IIS to send the X-Frame-Options header for all pages, add this to your web.config file:

``` xml
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Frame-Options" value="DENY" />
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

## Demo
To demonstrate the effectiveness of the X-Frame-Options header, we can create a parent html page, and a child html page that is embedded within a frame in the parent page.

### Parent HTML Page
Parent HTML page includes the iframe of the child page. The child page is hosted on a different domain (http://localhost:3333/child.html) to demonstrate the effectiveness of the X-Frame-Options header. 

``` html
<!DOCTYPE html>
<html>
    <head>
        <title>Parent Page</title>
    </head>
    <body>
        <h1>Parent Page</h1>
        <p>This is parent page. below is the iframe of child page.</p>
        <iframe src="http://localhost:3333/child.html" frameborder="0" sandbox="allow-scripts" style="width: 100%; height: 200px;"></iframe>
    </body>
</html>
```

### Child HTML Page
Child HTML page is a simple page that displays a message. It is hosted on the domain (http://localhost:3333/child.html) by using `httpster` tool.

``` html
<!DOCTYPE html>
<html>
    <head>
        <title>Child Page</title>
    </head>
    <body>
        <h1>Child Page</h1>   
        <p>This is a child page.</p> 
    </body>
</html>
```

### Testing
To test the effectiveness of the X-Frame-Options header, we can open the parent page in a browser and observe the behavior.

#### Without X-Frame-Options Header
By default, the `httpster` does not add the X-Frame-Options header to the response. Therefore, the child page can be embedded within a frame on the parent page. See below screenshot, these is no X-Frame-Options header in the response.

{% image /assets/images/frontend/without-x-frame-options-response-header.png, width=500px, alt=Without X-Frame-Options Header %}

#### With X-Frame-Options Header
With the X-Frame-Options header set to DENY, the child page cannot be embedded within a frame on the parent page.

To test the X-Frame-Options header, we need to modify the `httpster` server source code to add the X-Frame-Options header to the response. Actually, the `httpster` tool is a simple HTTP server base on node express. We can modify the `app.use` function to set the X-Frame-Options header in the httpster source code. 

Here is the modified `app.use` function with the X-Frame-Options header set to DENY:

``` javascript
app.use((req, res, next) => {
   res.setHeader('X-Frame-Options', 'DENY');
   next();
});
```

We can then open the parent page in a browser and observe the behavior. See below screenshot, these is with X-Frame-Options header value set to DENY in the response. And the child page is blocked from being embedded within a frame on the parent page.

{% image /assets/images/frontend/with-x-frame-options-response-header-deny.png, width=500px, alt=With X-Frame-Options Header Value DENY %}

And also, there is error message in the console of the browser, which indicates that the child page is blocked from being embedded within a frame on the parent page.

{% image /assets/images/frontend/with-x-frame-opitons-response-header-deny-console.png, width=500px, alt=With X-Frame-Options Header Value DENY Console Error %}

You can also test the X-Frame-Options header with different values such as SAMEORIGIN and ALLOW-FROM uri to see how it affects the behavior of the website.

## Conclusion
By implementing the X-Frame-Options header, web developers can enhance the security of their websites and protect users from potential clickjacking attacks. It is recommended to set this header appropriately based on the specific requirements of the web application.

Remember to test the effectiveness of the header using browser developer tools and security testing tools to ensure that it is properly configured.

