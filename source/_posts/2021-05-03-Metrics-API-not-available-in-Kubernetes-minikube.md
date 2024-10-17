---
title: Metrics API not available in Kubernetes minikube
date: 2021-05-03 16:57:20
categories: [DevOps]
tags: [Kubernetes, Cloud Native]
---

## 问题
当完成安装`minikube`并启动集群后，如果我们直接使用`kubectl top`命令去查看node的资源信息，会提示`metrics-server`服务不可用。

``` shell
kubectl top node
```

{% image /assets/images/k8s/k8s-metrics-api-not-available.png %}


## 解决方法
通常情况下，`minikube`是不自带`metrics-server`的，需要手动安装。 

### Step 1： 查看metrics-server的Pod是否存在

``` shell
kubectl get pods -n kube-system 
```

执行完这个命令后发现没有`metrics-server`的Pod存在。

{% image /assets/images/k8s/k8s-metrics-server-not-exist.png %}

### Step 2： 安装metrics-server
执行下面命令安装`metrics-server`组件：

``` shell
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

安装完成后，执行`kubectl top node`命令，仍然提示`Metrics API not available`。 通过以下命令查看`metrics-server`的deployment状态:

``` shell
kubectl -n kube-system get deployment metrics-server
```

`READY`状态为`0/1`，表示`metrics-server`组件未正常运行。 

{% image /assets/images/k8s/k8s-metrics-server-install.png %}

通过以下命令查看`metrics-server`的log日志信息

``` shell
kubectl -n kube-system logs deployment/metrics-server
```

{% image /assets/images/k8s/k8s-metrics-server-logs.png %}

服务器日志中显示`metrics-server`组件启动失败，原因是`failed to verify certificate`。这种情况下，我们可以更改`metrics-server`组件的启动参数，添加`--kubelet-insecure-tls`参数，以跳过证书验证。将下面的`command`参数添加到`metrics-server`的`Deployment`中：

``` yaml
command:
- /metrics-server
- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP
```

运行以下命令将`metrics-server`的component.yaml文件下载下来然后添加上面的参数.

``` shell
curl -L https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml -o components.yaml
```

编辑`components.yaml`文件，找到`metrics-server`的`Deployment`部分，添加上面的参数。

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.1
        imagePullPolicy: IfNotPresent
        command:
        - /metrics-server
        - --kubelet-insecure-tls
        - --kubelet-preferred-address-types=InternalIP
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 10250
          name: https
          protocol: TCP
  .................
```

保存文件，然后运行以下命令安装`metrics-server`组件。

``` shell
kubectl apply -f components.yaml
```

### Step 3: Stop minikube
安装完`metrics-server`组件后，需要停止`minikube`集群。

``` shell
minikube stop
```

### Step 4: Start minikube
启动`minikube`集群。

``` shell
minikube start
```

### Step 5: 检查 metrics-server的状态
执行`kubectl top node`命令，查看node的资源信息。可以看到这次就运行正常了。

``` shell
kubectl top node
```

{% image /assets/images/k8s/k8s-metrics-server-available.png %}

## 总结
`minikube`默认情况下是不自带`metrics-server`组件的，需要手动安装。安装完成后，需要停止`minikube`集群，然后启动集群，才能正常使用`kubectl top`命令查看node的资源信息。