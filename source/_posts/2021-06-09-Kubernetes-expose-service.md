---
title: Kubernetes 暴露 Service 给外部访问
date: 2021-06-09 11:13:36
categories: [DevOps, Cloud Native]
tags: [Kubernetes, Cloud Native]
---

在现代的容器化应用部署中，`Kubernetes` 已经成为了一个不可或缺的工具。它提供了一种高效的方式来管理和编排容器化的应用。在 Kubernetes 中，`Service` 是一个核心概念，它定义了一种访问一组 `Pod` 的策略，通常被称为微服务。本文将详细介绍 Kubernetes 中的 Service 是什么，如何将其暴露出来，以及外部如何访问这些服务。

## 什么是 Service？
在 Kubernetes 中，Service 是一种抽象，它定义了一组 Pod 的逻辑集合和一个访问它们的策略。Service 通过标签选择器（Label Selector）来确定它所服务的 Pod。{% pbg danger, 每个 Service 都有一个唯一的 IP 地址和端口，这使得即使 Pod 的 IP 地址发生变化，客户端也能通过 Service 稳定地访问到这些 Pod。%}

{% image /assets/images/k8s/k8s-service.png, width=800px, alt="Kubernetes Service" %}

## Service 的作用 
Service 的作用主要有以下几点：

1. 负载均衡：Service 提供了一种负载均衡的策略，通过它可以将流量分发到多个 Pod。
2. 服务发现：Service 提供了一种服务发现的机制，使得客户端应用能够通过名字来访问服务。
3. 命名空间隔离：Service 使得 Pod 和 Service 能够被隔离到不同的命名空间中，从而实现更细粒度的权限控制。
4. 跨集群服务：Service 提供了一种跨集群服务的能力，使得应用能够被部署到不同的 Kubernetes 集群中。

## Service 的类型
Kubernetes 提供了几种不同类型的 Service，以满足不同的需求：

### ClusterIP
这是默认的 Service 类型，它为 Service 分配一个集群内的 IP 地址。这种类型的 Service 只能在集群内部访问。

{% image /assets/images/k8s/k8s-service-clusterip.png, width=600px, alt="Kubernetes ClusterIP Service" %}

### NodePort
这种类型的 Service 会在每个节点的 IP 上打开一个特定的端口，并将该端口的流量转发到 Service。这样，外部客户端可以通过节点的 IP 和这个端口来访问 Service。

{% image /assets/images/k8s/k8s-service-nodeport.png, width=600px, alt="Kubernetes NodePort Service" %}

### LoadBalancer
这种类型的 Service 会在云提供商（如 AWS、GCP 或 Azure）上创建一个负载均衡器，并将流量转发到 Service。负载均衡器会为 Service 分配一个外部 IP 地址，外部客户端可以通过这个 IP 地址访问 Service。

{% image /assets/images/k8s/k8s-service-loadbalancer.png, width=600px, alt="Kubernetes LoadBalancer Service" %}

### ExternalName - Ingress
这种类型的 Service 通过返回一个 CNAME 记录，将 Service 映射到一个外部的 DNS 名称。

{% image /assets/images/k8s/k8s-service-external-name-ingress.png, width=800px, alt="Kubernetes ExternalName Ingress Service" %}

## 暴露 Service
### yaml 文件方式1
要暴露一个 Service，首先需要创建一个 Service 的 YAML 文件。以下是一个简单的 NodePort Service 的示例：

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30007
```

在这个示例中，我们定义了一个名为 my-service 的 Service，它的类型是 NodePort。这个 Service 会选择所有带有标签 app=my-app 的 Pod，并将端口 80 的流量转发到 Pod 的端口 8080。节点上的端口 30007 会被打开，外部客户端可以通过节点的 IP 和这个端口来访问 Service。

### kubectl 命令方式

``` bash
kubectl expose deployment my-deployment --port=80 --target-port=8080 --type=NodePort --name=my-service
```

在上面的命令中，使用 `kubectl expose` 命令来暴露一个 Deployment 的 Service。我们指定了 Deployment 的名称 my-deployment，端口 80，目标端口 8080，Service 类型为 NodePort，Service 名称为 my-service。

## 外部如何访问 Service
根据 Service 的类型，外部访问的方式也会有所不同：

**NodePort**：外部客户端可以通过节点的 IP 地址和 NodePort 指定的端口来访问 Service。例如，如果节点的 IP 地址是 192.168.1.100，NodePort 是 30007，那么外部客户端可以通过 http://192.168.1.100:30007 来访问 Service。

**LoadBalancer**：外部客户端可以通过负载均衡器分配的外部 IP 地址来访问 Service。例如，如果负载均衡器分配的 IP 地址是 52.10.10.10，那么外部客户端可以通过 http://52.10.10.10 来访问 Service。

## 总结
Kubernetes 中的 Service 提供了一种灵活且强大的方式来管理和访问一组 Pod。通过不同的 Service 类型，我们可以根据需求选择合适的方式来暴露和访问这些服务。无论是通过 NodePort 还是 LoadBalancer，Kubernetes 都能确保我们的应用能够被稳定且高效地访问。
