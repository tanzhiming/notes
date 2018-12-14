# 服务

## 定义一个服务

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
```


定义一个没有选择器的服务

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
```

没有选择器，需要手工定义端点

```yaml
kind: Endpoints
apiVersion: v1
metadata:
  name: my-service
subsets:
  - addresses:
      - ip: 1.2.3.4
    ports:
      - port: 9376
```


多端口服务

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 9376
  - name: https
    protocol: TCP
    port: 443
    targetPort: 9377
```

服务发现

1. 环境变量

kubernetes在pod中为激活的服务添加环境变量

如 "redis-master"服务，它暴露的端口为6379，集群IP 10.0.0.11

```
REDIS_MASTER_SERVICE_HOST=10.0.0.11
REDIS_MASTER_SERVICE_PORT=6379
REDIS_MASTER_PORT=tcp://10.0.0.11:6379
REDIS_MASTER_PORT_6379_TCP=tcp://10.0.0.11:6379
REDIS_MASTER_PORT_6379_TCP_PROTO=tcp
REDIS_MASTER_PORT_6379_TCP_PORT=6379
REDIS_MASTER_PORT_6379_TCP_ADDR=10.0.0.11
```

2. DNS

kubernetes为每个服务添加一条DNS记录

服务："my-service"， 名称空间： my-ns， DNS记录为"my-service.my-ns"


## Headless services

.spec.clusterIP 为 "None"

有选择器：

端点控制器创建端点， 添加A记录指向pods

没有选择器：

不创建端点， DNS系统查询和配置两者之一

* CNAME  为ExternalName-type services.
* A 记录  与服务共享名称的任何端点


## 服务类型

* ClusterIP： 默认， 集群内部IP
* NodePort： 通过节点IP暴露服务
* LoadBalancer：云提供商负载均衡
* ExternalName: 映射服务到外部名称


# DNS

每一个服务都分配一个DNS名称

假设: 服务名 foo, 名称空间bar， 一个运行在名称空间bar的pod， 简单使用foo查找， 运行在名称空间quux需要使用foo.bar 查找

## 服务

A 记录

正常服务（非无头服务): my-svc.my-namespace.svc.cluster.local 解析集群IP
无头服务： my-svc.my-namespace.svc.cluster.local 解析到Pods的IPs， 客户端通过轮询访问pod

SRV记录

## Pods

A 记录

pod-ip-address.my-namespace.pod.cluster.local

如 pod ip为 1.2.3.4  记录为1-2-3-4.default.pod.cluster.local

pod's 主机名和子域

```yaml
apiVersion: v1
kind: Service
metadata:
  name: default-subdomain
spec:
  selector:
    name: busybox
  clusterIP: None
  ports:
  - name: foo # Actually, no port is needed.
    port: 1234
    targetPort: 1234
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox1
  labels:
    name: busybox
spec:
  hostname: busybox-1
  subdomain: default-subdomain
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    name: busybox
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox2
  labels:
    name: busybox
spec:
  hostname: busybox-2
  subdomain: default-subdomain
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    name: busybox
```

### Pod's DNS Policy

* “Default“: 从节点继承名称解析配置
* “ClusterFirst“：
* “ClusterFirstWithHostNet“:
* “None“: 


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
  hostNetwork: true
  dnsPolicy: ClusterFirstWithHostNet
```

### Pod’s DNS Config

dnsConfig 字段
* nameservers
* searches
* options

```yaml
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 1.2.3.4
    searches:
      - ns1.svc.cluster.local
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```


# 连接应用

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80

```

## 创建服务

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
```

## 访问服务

环境变量

```yaml
$ kubectl exec my-nginx-3800858182-jr4a2 -- printenv | grep SERVICE
KUBERNETES_SERVICE_HOST=10.0.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
```

DNS

```
$ kubectl run curl --image=radial/busyboxplus:curl -i --tty
Waiting for pod default/curl-131556218-9fnch to be running, status is Pending, pod ready: false
Hit enter for command prompt
```

```
[ root@curl-131556218-9fnch:/ ]$ nslookup my-nginx
Server:    10.0.0.10
Address 1: 10.0.0.10

Name:      my-nginx
Address 1: 10.0.162.149
```



# Ingress

管理外部访问集群内服务


## 术语

* Node
* Cluster
* Edge router
* Cluster network
* Service


## Ingress controllers

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        backend:
          serviceName: test
          servicePort: 80
```


# 网路策略

