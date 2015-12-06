---
layout: post
title: "Using Nginx in Mac OS X"
date: 2015-12-05 11:51:04 +0800
comments: true
categories: [Nginx, DevOps, tools]
---

####Introduction
**Nginx** is a open source quite lightweight HTTP server which is written by Russian *Igor Sysoev*, the pronunciation is "engine X". It's a high performance HTTP and reverse proxy server, and also it is a IMAP/POP3/SMTP proxy server。

There are lots of website using Nginx in China, like Sina, Tencent, 163, Discuz and so on. The Nginx has a very high performance due to event driven way, and also it is a high performance reverse proxy, load balancing.

<!-- more -->

The features of Nginx Http server are below:

1.dealing with static file, indexing file and also automatically indexing.

2.accelerating reverse proxy server,load balancing

3.FastCGI, Caching, Security

4.Support SSL and TLS

From investigation, the Nginx can support about 50,000 concurrence request connection, and also support hot deploy, even running 7*24 hours uninterrupted.   

####Installing
So, how to install it on Mac OS X. Let's assume you have installed the *Home brew*, using brew to install the nginx with command:

```
brew install nginx
```

####Starting And Runing
After install, we can use below command to run it:

```
sudo nginx
```

If you get the failed message which is "[emerg] bind() to 0.0.0.0:8080 failed (48: Address already in use)". You have to kill the progress which is running with that address, it usually is Apache. And also there is another way, changing the port number, we will introduce later.

The nginx running success, you will get the below page when you access the [http://localhost:8080](http://localhost:8080)

![nginx, devops](/assets/images/nginx-welcome.png)

There are several commands to start, stop nginx and reload configuration.

``` 
nginx -s stop 			#=> fast shutdown

nginx -s quit				#=> graceful shutdown

nginx -s reload			#=> reloading the configuration file

nginx -s reopen			#=> reopening the log files
```

####Simple Configuration

The default place of nginx.conf on Mac after installing with brew is:

``` 
/usr/local/etc/nginx/nginx.conf
```
Let's change the port number in this file. First, we need to stop the nginx.

```
sudo nginx -s stop
```

And then open that file, you will see the below server configuration section. It includes the listen, server_name, location/root, location/index. location/root is your website root folder, and index is your website first page file.

```
server {
listen       8080;			
server_name  localhost;

#access_log  logs/host.access.log  main;

location / {
    root   html;
    index  index.html index.htm;
}

```

okay, let's change the port number from 8080 to 80, and then run it again

```
sudo nginx
```

Access it with [http://localhost](http://localhost), the 80 port number is http default port number, so we don't need to append it into url.

Now, let's put our blog website in the nginx, to do so, we need to reference the location/root to our blog website folder.

``` 
server {
listen       80;			
server_name  localhost;

#access_log  logs/host.access.log  main;

location / {
    root   /Users/yushi/Codes/Blog/stonefishy.github.io/public;;
    index  index.html index.htm;
}
```

let's running it again with clicking [http://localhost](http://localhost). Woow, cool, very nice, we can access our blog with nignx.

This is a just simple usage of nginx, we will talk about more later.





