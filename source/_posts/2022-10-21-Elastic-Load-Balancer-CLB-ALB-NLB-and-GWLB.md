---
title: 'Elastic Load Balancer: CLB, ALB, NLB and GWLB'
date: 2022-10-21 20:23:06
categories: Cloud Native
tags: [Cloud, AWS]
---

## Classic Load Balancers (v1)

**Supports TCP (Layer 4), HTTP & HTTPS (Layer 7)**

**Health checks are TCP or HTTP based**

**Fixed hostname XXX.region.elb.amazonaws.com**

{% image /assets/images/aws/aws-elb-clb.webp, width=600px, alt=AWS Classic Load Balancer %}

## Application Load Balancer (v2)

**Application  load balancers is Layer 7 (HTTP)**

**Load balancing to multiple HTTP applications across machines (target groups)**

**Load balancing to multiple applications on the same machine (ex: containers)**

**Support for HTTP/2 and WebSocket**

**Supports redirects (from HTTP to HTTPS for example)**

**Routing tables to different target groups:**
1. Routing base on path in URL (example.com/**users** & example.com/**posts**)
2. Routing base on hostname in URL (**one**.example.com & **other**.example.com)
3. Routing base on Query String, Headers (example.com/users?**id=123&order=false**)

{% pbg danger, ALB are a great fit for micro services & container-based application (example: Docker & Amazon ECS) %}
Has a port mapping feature to redirect to a dynamic port in ECS
In comparison, we'd need multiple Classic Load Balancer per application

### ALB (v2) HTTP Based Traffic

{% image /assets/images/aws/aws-elb-alb-traffic.webp, width=600px, alt=AWS Application Load Balancer Traffic %}

### ALB (v2) Target Groups

**EC2 instances (can be managed by an Auto Scaling Group) - HTTP**

**ECS tasks (managed by ECS itself) - HTTP**

**Lambda functions - HTTP request is translated into a JSON event**

**IP Addresses - must be private IPs**

**ALB can route to multiple target groups**

**Health checks are at the target group level**

{% image /assets/images/aws/aws-elb-alb-targetgroup.webp, width=600px, alt=AWS ALB Query Strings/Parameters Routing %}

## Network Load Balancer (v2)
**Network load balancers (Layer 4) allow to:**
1. Forward TCP & UDP traffic to your instances
2. Handle millions of request per seconds
3. Less latency ~ 100ms (vs 400 ms for ALB)

**NLB has one static IP per AZ, and supports assigning Elastic IP(helpful for whitelisting specific IP)**

**NLB are used for extreme performance, TCP or UDP traffic**

**Not included in the AWS free tier**

### Network Load Balancer (v2) TCP (Layer 4) Based Traffic
{% image /assets/images/aws/aws-elb-nlb-traffic.webp, width=600px, alt=AWS NLB TCP Based Traffic%}

### Network Load Balancer - Target Groups
**EC2 instances**

**IP Addresses - must be private IPs**

**Application Load Balancer**

**Health Checks support the TCP, HTTP and HTTPS protocols**

{% image /assets/images/aws/aws-elb-nlb-targetgroup.webp, width=600px, alt=AWS Network Load Balancer Target Group%}

## Gateway Load Balancer
**Deploy, scale, and manage a fleet of 3rd party network virtual applications in AWS Example: Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspect Inspection Systems, payload manipulation, ...**

**Operates at Layer 3 (Network Layer) - IP Packages**

**Combines the following fuctions:**
1. Transparent Network Gateway - single entry/exit for all traffic
2. Load Balancer - distributes traffic to your virtual applications
3. Uses the GENEVE protocol on port 6081

### Gateway Load Balancer Network Layer (Layer 3) Based Traffic
{% image /assets/images/aws/aws-elb-gwlb.webp, height=500px, alt=AWS Gateway Load Balancer Traffic%}  

### Gateway Load Balancer - Target Group
**EC2 instances**

**IP Addresses - must be private IPs**

{% image /assets/images/aws/aws-elb-gwlb-targetgroup.webp, width=600px, alt=AWS Gateway Load Balancer Target Group%}



