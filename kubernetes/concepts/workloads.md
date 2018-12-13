# Pods

## 理解Pods

* 运行单个容器
* 运行多个一起工作的容器

[pod diagram](../images/001.png)


**网络**
pod内容器可以localhost通信

**存储**


## Pods和Controllers

* Deployment
* StatefulSet
* DaemonSet

## Pod Templates


## Pod 生命周期

### Pod phase

* Pending
* Running
* Succeeded
* Failed
* Unknown

### Pod condition

### Container probes

* ExecAction
* TCPSocketAction
* HTTPGetAction

每个探针返回三个结果之一：
* Success
* Failure
* Unknown


livenessProbe 容器是否在运行
readinessProbe 容器服务请求是否就绪


## Init Containers

在app容器启动之前运行

初始容器和普通容器相似，除了

* 总是运行到完成
* 每一个初始容器必须在下一个启动之前完成


## 初始容器用于什么

* 出于安全原因，它们可以包含和运行不希望在应用程序容器镜像中包含的实用程序
* 他们包含一些app镜像中不存在的实用工具来进行设置
* 应用程序镜像构建者和部署器角色可以独立工作，而不需要联合构建单个应用程序镜像
* 它们使用Linux名称空间，因此它们具有不同于应用程序容器的文件系统视图。因此，它们可以访问应用程序容器无法访问的秘密。
* 它们在任何应用程序容器启动之前运行到完成，而应用程序容器是并行运行的，因此Init容器提供了一种简单的方法来阻止或延迟应用程序容器的启动，直到满足某些先决条件


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```

## Pod Preset



# 控制器

## ReplicaSet

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
    matchExpressions:
      - {key: tier, operator: In, values: [frontend]}
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
          # If your cluster config does not include a dns service, then to
          # instead access environment variables to find service host
          # info, comment out the 'value: dns' line above, and uncomment the
          # line below.
          # value: env
        ports:
        - containerPort: 80
```

## ReplicationController

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```


## Deployments

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.4
        ports:
        - containerPort: 80
```


## StatefulSets

## DaemonSet



