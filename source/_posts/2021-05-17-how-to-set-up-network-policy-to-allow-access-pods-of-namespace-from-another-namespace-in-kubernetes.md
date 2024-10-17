---
title: Kubernetes Network Policy Pod之间的网络策略通信
date: 2021-05-17 15:32:53
categories: [DevOps]
tags: [Kubernetes, Cloud Native]
---

## Network Policy 介绍
`Kubernetes`的`Network Policy`是用于定义和控制Pod之间通信的一种机制。它可以让您定义网络策略，指定哪些Pod可以与其他Pod通信，以及允许的通信方式。如下图，我们可以在MySQL Pod中创建一个Network Policy，仅允许来自HelloApp Pod的流量访问后端MySQL数据库，拒绝Rogue Pod的访问。

{% image /assets/images/k8s/k8s-network-policy.png, width=800px, Alt="Kubernetes Network Policy" %}

### 作用：

1. **网络隔离和安全性增强**：
  允许实现微服务架构中的网络隔离，确保只有经过授权的Pod可以相互通信。
  减少了攻击面，提高了集群的安全性，防止未经授权的访问和信息泄漏。

2. **细粒度的网络流量控制**：
  可以定义细致的规则，例如允许从一个标签选择器的Pod通信到另一个标签选择器的Pod，或者限制来自特定IP地址范围的流量。
  支持的规则包括允许/拒绝特定协议（如TCP、UDP）、端口号或IP块之间的通信。

3. **遵循最小权限原则**：
  可以根据应用程序的需求设置最小必要的访问权限，防止不必要的网络访问和流量。

4. **优化网络性能**：
  通过减少不必要的网络流量，有助于提高网络性能和资源利用率。

### 使用场景：
- **多租户环境**：在多个团队或项目共享同一个Kubernetes集群时，可以使用Network Policy来确保彼此之间的隔离和安全。
- **符合法规要求**：有一些国家的法规要求针对特定的系统需要网络隔离和数据保护。
- **微服务架构**：在微服务架构中，不同服务之间的通信需要精细控制，只允许具有特定标签的服务之间进行HTTP通信，同时限制其他通信类型或服务，Network Policy提供了这种能力。
- **数据保护**：保护包含敏感信息的Pod，确保只有授权的服务可以访问这些Pod。也可以限制来自特定IP地址或IP地址范围的流量访问集群中的Pod。

## 实战
### 相同命名空间的Pod访问
我们创建一个新的命名空间`my-app-ns`和两个Pod来演示Network Policy的使用. 首先创建两个Pod，分别是`app1 Pod`和`app2 Pod`。`image`这里都使用`nginx`镜像来演示，实际上可以是任何需要通信的容器。本地环境nginx镜像之前已经下载好了，可以直接使用。我们这里重点是Network Policy的创建。

``` bash
# 创建my-app-ns命名空间
kubectl create namespace my-app-ns

# 创建HelloApp Pod
kubectl -n my-app-ns run app1 --image=nginx

# 创建MySQL Pod
kubectl -n my-app-ns run app2 --image=nginx
```

{% image /assets/images/k8s/k8s-network-policy-app1-app2.png, Alt="Kubernetes Create Pods" %}

创建Network Policy，允许`app1 Pod`访问`app2 Pod`，这里我们不使用命令创建时自动添加的`label`标签，我们可以为每个Pod创建一个新的标签。查看Pod的标签可以使用`kubectl get pod --show-labels`命令。

``` bash
kubectl get pod -n my-app-ns --show-labels
```

{% image /assets/images/k8s/k8s-network-policy-pod-show-labels.png, Alt="Kubernetes Show Pod Label" %}

如下图，创建Pod时已经添加了标签`run=app1`和`run=app2`，我们可以为`app1 Pod`创建一个新的标签`app=app1-access`，并为`app2 Pod`创建一个新的标签`app=app2-access`。给已存在的Pod添加`label`标签使用以下命令

``` bash
kubectl label pod app1 app=app1-access -n my-app-ns
kubectl label pod app2 app=app2-access -n my-app-ns
```

{% image /assets/images/k8s/k8s-network-policy-label-app1-app2.png, Alt="Kubernetes Label Pods" %}

创建Network Policy，允许`app1-access`标签的Pod访问`app2-access`标签的Pod。创建一个名为`allow-app1-access-app2.yaml`的文件，内容如下：

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app1-access-app2 # 网络策略名称
  namespace: my-app-ns # 命名空间
spec:
  podSelector:
    matchLabels:
      app: app2-access # 选择器，匹配app2-access标签的Pod
  ingress: # 允许的入站流量
  - from:
    - podSelector:
        matchLabels:
          app: app1-access # 允许来自app1-access标签的Pod的流量
```

执行 `kubectl apply -f <yaml-file>` 命令创建Network Policy。

``` bash
kubectl apply -f allow-app1-access-app2.yaml
```

{% image /assets/images/k8s/k8s-network-policy-create.png, Alt="Kubernetes Allow App1 Access App2" %}

上面显示的`allow-app1-access-app2.yaml`文件定义了一个名为`allow-app1-access-app2`的网络策略，它允许`app1-access`标签的Pod访问`app2-access`标签的Pod的任意端口号。现在我们更新下`Network Policy`仅允许app1访问app2的80端口，更新yaml文件`allow-app1-access-app2.yaml`的文件，在`ingress`下添加`port`字段，内容如下：

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app1-access-app2-port80 # 网络策略名称
  namespace: my-app-ns # 命名空间
spec:
  podSelector:
    matchLabels:
      app: app2-access # 选择器，匹配app2-access标签的Pod
  ingress: # 允许的入站流量
  - from:
    - podSelector:
        matchLabels:
          app: app1-access # 允许来自app1-access标签的Pod的流量
    ports:
    - port: 80 # 允许的端口号
      protocol: TCP # 允许的协议
```

重新执行`kubectl apply -f <yaml-file>`命令更新Network Policy。

``` bash
kubectl apply -f allow-app1-access-app2.yaml
```

使用`kubectl describe networkpolicy`命令查看Network Policy的详细信息。

``` bash
kubectl describe networkpolicy allow-app1-access-app2 -n my-app-ns
```

{% image /assets/images/k8s/k8s-network-policy-port.png, Alt="Kubernetes Allow App1 Access App2 Port 80" %}

至此，我们就创建了一个在同一个命令空间`my-app-ns`中，允许`app1-access`标签的Pod访问`app2-access`标签的Pod的`80`端口的Network Policy。

### 不同命名空间的Pod访问

现在我们来演示不同命名空间的Pod访问。我们创建一个新的命名空间`my-app-ns2`和一个名为`app3`的`Pod`， `app3` Pod和`app1`，`app2` Pod在不同的命名空间中，我们需要允许命令空间`my-app-ns2`中的`app3` Pod访问命令空间`my-app-ns`中的任何pod。访问端口号为9000

``` bash
# 创建my-app-ns2命名空间
kubectl create namespace my-app-ns2

# 创建app3 Pod
kubectl -n my-app-ns2 run app3 --image=nginx
```

{% image /assets/images/k8s/k8s-network-policy-app3.png, Alt="Kubernetes Create App3 Pod" %}

我们为`app3` Pod创建一个新的标签`app=app3-access`.

``` bash
kubectl label pod app3 app=app3-access -n my-app-ns2
```

创建Network Policy，允许`app3-access`标签的Pod访问`app2-access`标签的Pod。创建一个名为`allow-ns2-app3-access-ns-apps.yaml`的文件，内容如下：

``` yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ns2-app3-access-ns-apps # 网络策略名称
  namespace: my-app-ns # 被访问的命名空间
spec:
  podSelector: {} # 选择器，匹配被访问命令空间的所有Pod
  ingress: # 允许的入站流量
  - from:
    - namespaceSelector:
        matchLabels:
          name: my-app-ns2 # 允许来自my-app-ns2命名空间的Pod的流量
    - podSelector:
        matchLabels:
          app: app3-access # 允许来自app3-access标签的Pod的流量
    ports:
    - port: 9000 # 允许的端口号
      protocol: TCP # 允许的协议
```

执行 `kubectl apply -f <yaml-file>` 命令创建Network Policy。

``` bash
# 创建Network Policy
kubectl apply -f allow-ns2-app3-access-ns-apps.yaml

# 查看Network Policy
kubectl describe networkpolicy allow-ns2-app3-access-ns-apps -n my-app-ns
```

{% image /assets/images/k8s/k8s-network-policy-access-ns-pods.png, Alt="Kubernetes Allow Ns2 App3 Access Ns Apps" %}

上面，我们创建了一个允许`my-app-ns2`命名空间中的`app3-access`标签的Pod访问`my-app-ns`命名空间中所有的Pod，允许的端口号是`9000`的Network Policy。 至此，我们就创建了一个允许不同命名空间的Pod访问另一个命名空间的Pod的Network Policy。

## 总结
Kubernetes的Network Policy提供了一种强大的方式来管理和保护您的容器化应用程序之间的网络通信。通过定义网络策略，可以实现微服务架构中的网络隔离，确保只有经过授权的Pod可以相互通信。同时，还可以限制来自特定IP地址范围的流量访问集群中的Pod，保护包含敏感信息的Pod，确保只有授权的服务可以访问这些Pod。