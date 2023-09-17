---
title: Elastic Load Balancing (ELB) Overview
date: 2022-10-17 16:01:25
categories: 云计算
tags: [云计算, AWS]
---

{% title h2, What is load balancing %}

Load Balances are servers that forward traffic to multiple servers (e.g., EC2 instances) downstream.

{% image /assets/images/aws/aws-elb-1.webp, width=600px, alt=AWS ELB %}

{% title h2, Why use a load balancing %}

- Spread load across multiple downstream instances
- Expose a single point of access (DNS) to your application
- Seamlessly handle failures of downstream instances
- Do regular health checks to your instances
- Provide SSL termination (HTTPS) for your websites
- Enforce stickiness with cookies
- Hight availability across zones
- Separate public traffic from private traffic

{% title h2, Why use an Elastic Load Balancer %}

- An Elastic Load Balancer is a managed load balancer
    -  AWS guarantees that it will be working
    - AWS takes care of upgrades, maintenance, hight availability
    - AWS provides only a few configuration knobs
- It costs less to setup your own load balancer but it will be a lot more effort on your end
- It is integrarted with many AWS offerings/services
    - EC2, EC2 Auto Scaling Groups, Amazon ECS
    - AWS Certificate Manager (ACM), CloudWatch
    - Route 53, AWS WAF, AWS Global Accelerator

{% title h2, Health Checks %}

- Health Checks are crucial for Load Balancers
- They enable the load balancer to know if instances it forwards traffic to are available to reply to requests
- The health check is done on a port and a route (/health is common)
- If the response is not 200 (OK), then the instance is unhealthy

{% image /assets/images/aws/aws-elb-health.webp, width=600px, alt=AWS ELB Health Check %}


{% title h2, Types of load balancer on AWS %}

- AWS has 4 kinds of managed Load Balancers
- Classic Load Balancer (v1 - old generation) - 2009 - CLB
    - HTTP, HTTPS, TCP, SSL (secure TCP)
- Application Load Balancer (v2 - new generation) - 2016 - ALB
    - HTTP, HTTPS, WebSocker
- Network Load Balancer (v2 - new generation) - 2017 - NLB
    - TCP, TLC (secure TCP), UDP
- Gateway Load Balancer - 2020 - GWLB
    - Operates at layer 3 (Network layer) - IP Protocol
- Overall, it is recommended to use the new generation load balancers as they provides more features
- Some load balancers can be setup as internal (private) or external (public) ELBs

{% title h2, Load Balancer Security Groups %}

{% image /assets/images/aws/aws-elb-security.webp, width=600px, alt=AWS ELB Security Group %}
