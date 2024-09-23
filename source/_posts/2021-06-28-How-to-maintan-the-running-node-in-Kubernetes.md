---
title: 如何维护正在运行的 Kubernetes 节点
date: 2021-06-28 10:02:01
categories: [DevOps, Cloud Native]
tags: [Kubernetes, Cloud Native]
---


`Kubernetes` 集群中的节点可以分为两种类型：
- 主节点：主节点是 Kubernetes 集群的控制节点，负责管理集群的控制平面组件，如 API 服务器、调度器、控制器管理器等。
- 工作节点：工作节点是 Kubernetes 集群的工作节点，负责运行容器化的应用。

当节点出现故障时，集群会自动将其从工作节点中移除，并将其上的容器调度到其他节点上。我们可以通过命令`kubectl get nodes`查看集群中所有节点的状态。

但是有些情况下，我们需要维护正在运行的节点，比如：升级节点的内核或操作系统， 修复节点上的软件包等等。这种情况下，为了不影响正在节点上运行的应用，在应用无感知的情况下，我们需要对节点进行维护。主要通过使用以下三个命令来实现：

- `kubectl cordon <node-name>`：将节点标记为不可调度。
- `kubectl drain <node-name>`：将节点上的工作负载转移到其他节点上。
- `kubectl uncordon <node-name>`：将节点标记为可调度。

我们这里用minikube来做示例，假设我们要维护正在运行的minikube节点。首先，查看节点的状态。

``` shell
kubectl get nodes -o wide
```

{% image /assets/images/k8s/k8s-maintain-node.png, alt="Kubernetes Get Nodes" %}
我们可以看到，minikube节点的状态是`Ready`，即节点处于正常状态。

## kubectl cordon
`kubectl cordon`的作用是将节点标记为不可调度。这意味着不会在该节点上调度新的Pod，但现有的Pod将继续运行。当我们准备对节点进行维护或升级时，可以使用此命令，暂时不想驱逐任何现有的Pod。

此时，我们通过此命令将节点标记为不可调度：

``` shell
kubectl cordon minikube
```

{% image /assets/images/k8s/k8s-maintain-cordon.png, alt="Kubernetes Cordon Node" %}
再次查看节点状态时，发现节点状态从`Ready`变成了`Ready,SchedulingDisabled`。这意味着节点已经被标记为不可调度，但是其中已调度的Pods仍然可以正常运行。

## kubectl drain
`kubectl drain`的作用是安全地从节点中驱逐所有`Pod`, 该命令将首先将节点标记为不可调度（cordon），然后尝试驱逐其上的所有Pod，确保它们被终止或重新调度到其他节点（如果它们是副本集、部署或其他控制器的一部分）, 特别适用于应用更新或进行硬件维护时。

``` shell
kubectl drain minikube
```

如何节点上有daemonset和local storage使用，直接执行上述的命令时会出现如下错误.

``` text
error: unable to drain node "minikube" due to error:[cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-system/kube-proxy-jsdtq,
cannot delete Pods with local storage (use --delete-emptydir-data to override): kube-system/metrics-server-c74bf77d6-9wpx5]
```

{% image /assets/images/k8s/k8s-maintain-drain-failure.png, alt="Kubernetes Drain Node Error" %}

此时，我们需要使用`--ignore-daemonsets`参数来忽略daemonset，并使用`--delete-emptydir-data`参数来删除节点上的local storage。

``` shell
kubectl drain minikube --ignore-daemonsets --delete-emptydir-data
```

{% image /assets/images/k8s/k8s-maintain-drain-success.png, alt="Kubernetes Drain Node Success" %}

此时，节点上的所有Pod都已被驱逐，但节点仍然处于`Ready,SchedulingDisabled`状态。 查看Pods的运行状态，Pods的状态已经从Running变成了Pending状态。

``` shell
kubectl get pods
```

{% image /assets/images/k8s/k8s-maintain-drain-status.png, alt="Kubernetes Drain Node Status" %}

在这个步骤之后，我们就可以对节点进行维护，比如升级内核或操作系统，修复软件包等。当维护完成后，我们就可以通过命令`kubectl uncordon`将节点标记为可调度，允许新的Pod在其上进行调度。


## kubectl uncordon
`kubectl uncordon`的作用是将先前已被标记为不可调度的节点重新标记为可调度，允许新的Pod在其上进行调度。这意味着在该节点上可以调度新的Pod。当维护完成后，可以使用此命令将节点标记为可调度。

``` shell
kubectl uncordon minikube
```

{% image /assets/images/k8s/k8s-maintain-uncordon.png, alt="Kubernetes Uncordon Node" %}

从上面可以看出，节点已经恢复成了`Ready`状态，可以正常调度Pod。并且Pod的状态也从`Pending`变成了`Running`。

## 总结
通过上述三个命令，我们可以对正在运行的Kubernetes节点进行维护。首先，我们通过命令`kubectl cordon`将节点标记为不可调度，然后通过命令`kubectl drain`将节点上的工作负载转移到其他节点上。最后，我们通过命令`kubectl uncordon`将节点标记为可调度，允许新的Pod在其上进行调度。这样，我们就可以在不影响正在运行的应用的情况下，对节点进行维护。




