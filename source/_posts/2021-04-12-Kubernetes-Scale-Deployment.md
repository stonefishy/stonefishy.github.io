---
title: Kubernetes Deployment 的伸缩性
date: 2021-04-12 10:11:09
categories: [DevOps]
tags: [Kubernetes, Cloud Native]
---

`Kubernetes` 是一个强大的容器编排平台，广泛应用于各种规模的`云原生应用`。在 Kubernetes 中，`Deployment` 是一种用于管理应用实例的高级资源对象。通过调整 Deployment 的副本数量，可以轻松实现应用的伸缩性，以满足不同的负载需求。本文将介绍如何在 Kubernetes 中使用 kubectl scale 命令来伸缩 Deployment。

## 什么是 Deployment？
在 Kubernetes 中，Deployment 是一种控制器，用于管理 Pod 的生命周期。它定义了应用的期望状态，并确保集群中的实际状态与期望状态保持一致。Deployment 的主要功能包括：

- **滚动更新**：逐步替换旧版本的 Pod，以实现无缝的应用更新。
- **回滚**：在更新过程中出现问题时，可以快速回滚到之前的版本。
- **副本管理**：自动调整 Pod 的副本数量，以满足应用的负载需求。

## 如何伸缩 Deployment？
伸缩 Deployment 的主要目的是根据应用的负载情况调整 Pod 的副本数量。在 Kubernetes 中，可以使用 `kubectl scale` 命令来实现这一目标。

### 基本命令

``` shell
kubectl scale deployment <deployment-name> --replicas=<number-of-replicas>
```

- `<deployment-name>`：Deployment 名称。
- `<number-of-replicas>`：副本数量。

### 示例
首先我们先创建一个deployment，用下面的命令

``` shell
kubectl create deployment my-deployment --image=kicbase/echo-server:1.0 
```

{% image /assets/images/k8s/k8s-create-deployment.png %}

从现在的截图中，看到的是目前只有1个副本。我们可以通过以下命令将其伸缩到 3 个副本：

``` shell
kubectl scale deployment my-deployment --replicas=3
```

执行上述命令后，Kubernetes 将自动创建 2 个新的 Pod，使总副本数量达到 3 个。如下图

{% image /assets/images/k8s/k8s-scale-deployment.png %}

可以看到，Kubernetes 已经创建了 2 个新的 Pod，并将它们加入到 Deployment 的管理之下。 另外，我们也可以直接编辑 Deployment 的配置文件，修改其 `spec.replicas` 字段，比如我现在可以将 `my-deployment` 的副本数量修改为 5：

``` shell
kubectl edit deployment my-deployment
```

通过上面个命令，我们可以直接编辑 `my-deployment` 的配置文件，修改其 `spec.replicas` 字段为 5。如下图

{% image /assets/images/k8s/k8s-edit-deployment.png %}

保存并退出编辑器，Kubernetes 就会根据新的副本数量，创建新的 Pod，并将它们加入到 Deployment 的管理之下。如下图

{% image /assets/images/k8s/k8s-edit-deployment-2.png %}

## 自动伸缩
除了手动伸缩外，Kubernetes 还支持`自动伸缩（Horizontal Pod Autoscaler，HPA）`。`HPA` 可以根据 `CPU` 使用率等指标自动调整 `Deployment` 的副本数量。

以下是一个创建 HPA 的示例：

``` yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: my-deployment-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 50
```

将上述 YAML 文件保存为 hpa.yaml，然后使用 `kubectl apply` 命令创建 HPA：

``` shell
kubectl apply -f hpa.yaml
```

创建 HPA 后，Kubernetes 将根据 CPU 使用率自动调整 my-deployoment Deployment 的副本数量，使其在 2 到 5 个副本之间动态变化。

{% image /assets/images/k8s/k8s-hpa-deployment.png %}


## 总结
通过 `kubectl scale` 命令和 `Horizontal Pod Autoscaler`，Kubernetes 提供了灵活且强大的伸缩机制，使应用能够根据负载情况自动调整资源分配。这不仅提高了应用的可用性和性能，还优化了资源利用率。无论是`手动伸缩`还是`自动伸缩`，Kubernetes 都能满足不同场景下的需求，为云原生应用的部署和管理提供了极大的便利。

