---
title: Kubernetes权限管理之ServiceAccount
date: 2021-03-22 15:48:39
categories: [DevOps, Cloud Native]
tags: [Kubernetes, Cloud Native]
---

在`Kubernetes`中，`ServiceAccount` 是一种用于为`Pod`中的进程提供身份标识的对象。它的主要作用是允许这些进程与`Kubernetes API`进行交互，从而实现各种`自动化任务`。

## ServiceAccount
ServiceAccount的主要作用包括：

- **身份验证**：当Pod中的进程需要与Kubernetes API服务器通信时，ServiceAccount提供了身份验证机制。每个ServiceAccount都有一个关联的Secret（包含一个Token），这个Token可以用来验证Pod的身份。

- **授权**：ServiceAccount可以与`Role`或`ClusterRole`绑定，通过RoleBinding或ClusterRoleBinding来定义权限。这样，Pod中的进程就可以根据其ServiceAccount的权限来执行特定的操作。

- **自动化任务**：通过ServiceAccount，Pod可以自动执行各种任务，例如获取集群状态、管理资源、部署应用等, 也可将其用于CI/CD流程。

在本文中，我们将介绍如何创建和使用`Kubernetes`中的`ServiceAccount`。

以下是一个创建ServiceAccount的yaml文件定义的示例：

``` yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-serviceaccount
  namespace: default
```

创建这个ServiceAccount后，你可以将其分配给Pod，以便Pod中的进程可以使用这个ServiceAccount来与Kubernetes API进行交互。

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: default
spec:
  serviceAccountName: my-serviceaccount
  containers:
  - name: my-container
    image: nginx
```

在上面这个例子中，example-pod 使用 example-serviceaccount 来进行身份验证和授权，进而可以与Kubernetes API服务器进行交互。通过这种方式，Kubernetes的ServiceAccount为Pod中的进程提供了一种安全且灵活的方式来与集群进行交互。

## Cluster Role
在`Kubernetes`中，`ClusterRole（集群角色）`是一种资源，用于定义集群范围内的权限。它允许管理员定义哪些操作可以在整个集群中执行，例如管理节点、获取所有命名空间的资源等。

`ClusterRole`可以通过Kubernetes的YAML定义文件来创建的。也可以通过命令来创建，以下是一个示例ClusterRole的定义：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: my-clusterrole
rules:
- apiGroups: [""]
  resources: ["Pods", "Deployments", "Services"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]
```

这个示例ClusterRole定义了一个名为 my-clusterrole 的角色，允许对集群中的"Pods", "Deployments"和"Services"资源执行get、list、watch、create、update和delete操作。

`ClusterRole`可以与`ServiceAccount`绑定，以便授予`ServiceAccount`特定的权限。`ClusterRoleBinding`定义了`ClusterRole`与`ServiceAccount`之间的绑定关系。

``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: my-clusterrolebinding
subjects:
- kind: ServiceAccount
  name: my-serviceaccount
  namespace: default
roleRef:
  kind: ClusterRole
  name: my-clusterrole
  apiGroup: rbac.authorization.k8s.io
```

这个示例ClusterRoleBinding将名为 my-serviceaccount 的ServiceAccount与名为 my-clusterrole 的ClusterRole绑定。通过这种方式，我们可以为`ServiceAccount`分配特定的`ClusterRole`，以便授予其特定的权限。这样，`ServiceAccount`就可以在整个集群中执行各种自动化任务，而无需为每个Pod都创建特定的权限。

## kubectl命令行创建示例
以下是使用命令行创建`ServiceAccount`、`ClusterRole`和`ClusterRoleBinding`的示例：

1. 首先，我们先创建一个名为my-namespace的namespace（命名空间）：
``` shell
kubectl create namespace my-namespace
```

2. 接着我们在这个命名空间中创建一个名为my-serviceaccount的ServiceAccount：
``` shell
kubectl -n my-namespace create serviceaccount my-serviceaccount
```

3. 然后再创建一个名为my-clusterrole的ClusterRole：
``` shell
kubectl create clusterrole my-clusterrole --verb=get,list,watch,create,update,delete --resource=pods,deployments,services
```

4. 最后创建一个名为my-clusterrolebinding的ClusterRoleBinding，将命名空间my-namespace中的my-serviceaccount与my-clusterrole绑定:
``` shell
kubectl -n my-namespace create clusterrolebinding my-clusterrolebinding --clusterrole=my-clusterrole --serviceaccount=my-namespace:my-serviceaccount
```

以下是执行命令的截图：

{% image /assets/images/k8s/k8s-clusterrolebinding.png %}

## kubectl命令行查看示例

1. 我们可以查看namespace的详细信息：
``` shell
kubectl get namespace my-namespace
```
输出应该如下所示：
```
NAME           STATUS   AGE
my-namespace   Active   1m
```

2. 我们可以查看一下这个`ServiceAccount`的详细信息：
``` shell
kubectl -n my-namespace get serviceaccount my-serviceaccount
```
输出应该如下所示：
```
NAME                SECRETS   AGE
my-serviceaccount   0         1m
```

3. 我们可以查看一下这个`ClusterRole`的详细信息：
``` shell
kubectl get clusterrole my-clusterrole
```
输出应该如下所示：
```
NAME            AGE
my-clusterrole   1m
```

4. 我们可以查看一下这个`ClusterRoleBinding`的详细信息：
``` shell
kubectl -n my-namespace get clusterrolebinding my-clusterrolebinding
```
输出应该如下所示：
```
NAME                   ROLE                          AGE
my-clusterrolebinding   ClusterRole/my-clusterrole   1m
```

下面就是整个过程的截图：

{% image /assets/images/k8s/k8s-describe-clusterrolebinding.png %}

这样，我们就创建了一个名为my-serviceaccount的`ServiceAccount`，它与名为my-clusterrole的`ClusterRole`绑定，并授予了它在my-namespace命名空间中执行各种自动化任务的权限。
