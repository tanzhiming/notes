# 什么是Kubernetes?

Kubernetes是一个可移植，可扩展的管理容器负载和服务的开源平台，促进了声明式配置和自动化。它有大量的，快速成长的生态。

## 为什么需要Kubernetes和它能做什么？

Kubernetes有一系列特性。它被认为是：
* 容器平台
* 微服务平台
* 可移植的云平台
* 其他

## Kubernetes不是什么

Kubernetes不是一个传统的、全面的PaaS(平台即服务)系统。由于Kubernetes是在容器级别而不是在硬件级别上运行的，因此它提供了一些PaaS产品中常见的通用特性，例如部署、伸缩、负载均衡、日志记录和监控。然而，Kubernetes并不是整体的，这些默认的解决方案是可选的和可插入的。Kubernetes为构建开发人员平台提供了构建模块，但在重要的地方保留了用户的选择和灵活性。

* 不限制所支持的应用程序类型。Kubernetes旨在支持非常多样化的工作负载，包括无状态、有状态和数据处理工作负载。如果应用程序可以在容器中运行，那么它应该在Kubernetes上运行良好。
* 不部署源代码，也不构建应用程序。持续集成、交付和部署(CI/CD)工作流由组织文化和首选项以及技术需求决定。
* 不提供应用程序级服务，如中间件(如消息总线)、数据处理框架(如Spark)、数据库(如mysql)、缓存或集群存储系统(如Ceph)作为内置服务。
* 没有规定日志记录、监控和告警解决方案。
* 不提供或要求配置语言/系统(如jsonnet)。它提供了一个声明性API，可以以任意形式的声明性规范为目标。
* 不提供或采用任何全面的机器配置、维护、管理或自愈系统。


# Kubernetes 组件

## Master组件

**Kube-apiserver**

暴露Kubernetes API。Kubernetes控制平面的前端。

**etcd**

一致性和高可用的键值存储，集群数据的存储

**kube-scheduler**

资源调度

**kube-controller-manager**

控制器管理器

控制器包括：

* Node Controller
* Replication Controller
* Endpoint Controller
* Service Account & Token Controllers

**cloud-controller-manager**

## Node 组件

**kubelet**

确保容器在pod中运行

**Kube-proxy**

通过在主机上维护网路规则和连接转发来实现服务抽象

**Container Runtime**

能够运行容器的软件：docker， rkt， runc等

## Addons（附件）

DNS

Web UI(Dashboard)

Container Resource Monitoring

Cluster-level Logging



# Kubernetes API

## API Version

* Alpha level

 版本包括alpha， 如v1alpha1

* Beta level

 版本名包括beta, 如v2beta3

* Stable level
 
 版本名是 vX, X为整数


## API groups

1. 核心组: REST PATH 为/api/v1， 使用 apiVersion: v1

2. 命名组：REST PATH 为/apis/$GROUP_NAME/$VERSION, 使用 apiVersion: $GROUP_NAME/$VERSION



# Kubernetes Objects

## Object Spec 和 Status

Spec：desired state 希望的对象状态
Status： 真实的对象状态

```yaml
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

```
$ kubectl create -f https://k8s.io/examples/application/deployment.yaml --record
```

必要字段

* apiVersion
* kind
* metadata


## Names

Names

UIDs


## Namespaces(名称空间)

查看名称空间

```
$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    1d
kube-system   Active    1d
kube-public   Active    1d
```

为请求设置名称空间
```
$ kubectl --namespace=<insert-namespace-name-here> run nginx --image=nginx
$ kubectl --namespace=<insert-namespace-name-here> get pods
```


设置名称空间偏好

```
$ kubectl config set-context $(kubectl config current-context) --namespace=<insert-namespace-name-here>
# Validate it
$ kubectl config view | grep namespace:
```


名称空间和DNS

服务创建时，会创建相应的DNS记录`<service-name>.<namespace-name>.svc.cluster.local`


查看资源是否在名称空间中

```
# In a namespace
$ kubectl api-resources --namespaced=true

# Not in a namespace
$ kubectl api-resources --namespaced=false
```

## Labels 和 Selectors

标签用来组织和选择对象子集

```yaml
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

示例标签：

* `"release"："stable", "release": "canary"`
* `"environment": "dev", "environment": "qa", "environment": "production"`
* `"tier" : "frontend", "tier" : "backend", "tier" : "cache"`
* `"partition" : "customerA", "partition" : "customerB"`
* `"track" : "daily", "track" : "weekly"`

标签选择器

Equality-based requirement 基于等式

=, ==, !=

```
environment = production
tier != frontend
environment=production,tier!=frontend
```

例：

```
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100
```
Set-based requirement 基于集合

in, notin, exists

例:

```
environment in (production, qa)
tier notin (frontend, backend)
partition
!partition
```

```
$ kubectl get pods -l environment=production,tier=frontend
```
```
$ kubectl get pods -l 'environment in (production),tier in (frontend)'
```

```
$ kubectl get pods -l 'environment in (production, qa)'
```

```
$ kubectl get pods -l 'environment,environment notin (frontend)'
```

Service and ReplicationController

```
"selector": {
    "component" : "redis",
}
```


Resources that support set-based requirements

```
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```


## Annotations

```
"metadata": {
  "annotations": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```


## Field Selectors

* metadata.name=my-service
* metadata.namespace!=default
* status.phase=Pending

```
$ kubectl get pods --field-selector status.phase=Running
```


支持操作

`=, ==, !=`

`$ kubectl get services --field-selector metadata.namespace!=default`

`$ kubectl get pods --field-selector=status.phase!=Running,spec.restartPolicy=Always`

`$ kubectl get statefulsets,services --field-selector metadata.namespace!=default`


# Kubernetes 对象管理

管理技术

1. 命令式
2. 命令式对象配置
3. 声明式对象配置


命令式：

```
kubectl run nginx --image nginx
```

```
kubectl create deployment nginx --image nginx
```


命令式对象配置

```
kubectl create -f nginx.yaml
```

```
kubectl delete -f nginx.yaml -f redis.yaml
```

```
kubectl replace -f nginx.yaml
```

声明式对象配置

```
kubectl diff -f configs/
kubectl apply -f configs/
```

递归处理

```
kubectl diff -R -f configs/
kubectl apply -R -f configs/
```



