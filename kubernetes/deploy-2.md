# 安装Kubernetes集群

## 节点规划
|主机名|IP地址|
|---|---|
|master|192.168.2.10|
|node1|192.168.2.20|
|node2|192.168.2.30|

## Docker安装

1. 如果有安装老版本Docker, 先卸载老版本docker

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

2. 设置yum源

```shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

```shell
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

3. 安装Docker-CE

```shell
sudo yum install docker-ce
```

4. 启动Docker

```shell
sudo systemctl start docker
```

5. 开机启动

```shell
sudo systemctl enable docker
```

## 安装kubeadm,kubelet 和 kubectl

1. 禁用防火墙

systemctl stop firewalld
systemctl disable firewalld

2. 安装 (所有节点)

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

systemctl enable kubelet && systemctl start kubelet
```


```shell
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sysctl --system
```

```shell
swapoff -a
```
注释掉/etc/fstab的swap

3. 初始化master节点 (master节点)

```shell
kubeadm init \
  --kubernetes-version=v1.12.3 \
  --pod-network-cidr=10.244.0.0/16 \
  --apiserver-advertise-address=192.168.2.10
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


安装flannel插件
```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```



4. 加入节点（node1， node2节点）

```shell
kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```

token和discovery-token-ca-cert-hash由主节点创建

token
```
kubeadm token create
```
discovery-token-ca-cert-hash 
```
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
```


5. （可选）非主节点控制集群

```
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf get nodes
```

6. (可选) 代理API Server

```shell
scp root@<master ip>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf proxy
```