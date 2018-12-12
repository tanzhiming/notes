
# kubernetes安装

### 安装Kubeadm, kubelet和kubectl

设置主机名

```
hostnamectl set-hostname master
```

/etc/hosts
```shell
192.168.0.10 master
192.168.0.20 node1
192.168.0.30 node2
```

禁用防火墙

systemctl stop firewalld
systemctl disable firewalld



```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

systemctl enable kubelet && systemctl start kubelet

```


```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```



```
swapoff -a && sed -i '/swap/d' /etc/fstab
```

预加载镜像
列出使用的镜像
```
kubeadm config images list --kubernetes-version=v1.12.3
```


images
```
k8s.gcr.io/kube-apiserver:v1.12.3=mirrorgooglecontainers/kube-apiserver:v1.12.3
k8s.gcr.io/kube-controller-manager:v1.12.3=mirrorgooglecontainers/kube-controller-manager:v1.12.3
k8s.gcr.io/kube-scheduler:v1.12.3=mirrorgooglecontainers/kube-scheduler:v1.12.3
k8s.gcr.io/kube-proxy:v1.12.3=mirrorgooglecontainers/kube-proxy:v1.12.3
k8s.gcr.io/pause:3.1=mirrorgooglecontainers/pause:3.1
k8s.gcr.io/etcd:3.2.24=mirrorgooglecontainers/etcd:3.2.24
k8s.gcr.io/coredns:1.2.2=coredns/coredns:1.2.2
```

load_images.sh
```shell
#/bin/bash

file="images"

if [ -f "$file" ]
then
  echo "$file found."

  while IFS='=' read -r key value
  do
    #echo "${key}=${value}"
    docker pull ${value}
    docker tag ${value} ${key}
    docker rmi ${value}
  done < "$file"

else
  echo "$file not found."
fi
```



```
kubeadm init \
  --kubernetes-version=v1.13.0 \
  --pod-network-cidr=10.244.0.0/16 \
  --apiserver-advertise-address=192.168.0.10 \
  --ignore-preflight-errors=Swap
```

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```


```
mkdir -p ~/k8s/
cd ~/k8s
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f  kube-flannel.yml
```



```
docker pull mirrorgooglecontainers/kube-apiserver:v1.13.0
docker pull mirrorgooglecontainers/kube-controller-manager:v1.13.0
docker pull mirrorgooglecontainers/kube-scheduler:v1.13.0
docker pull mirrorgooglecontainers/kube-proxy:v1.13.0
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/etcd:3.2.24
docker pull coredns/coredns:1.2.6

docker tag mirrorgooglecontainers/kube-apiserver:v1.13.0 k8s.gcr.io/kube-apiserver:v1.13.0
docker tag mirrorgooglecontainers/kube-controller-manager:v1.13.0 k8s.gcr.io/kube-controller-manager:v1.13.0
docker tag mirrorgooglecontainers/kube-scheduler:v1.13.0 k8s.gcr.io/kube-scheduler:v1.13.0
docker tag mirrorgooglecontainers/kube-proxy:v1.13.0 k8s.gcr.io/kube-proxy:v1.13.0
docker tag mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag mirrorgooglecontainers/etcd:3.2.24 k8s.gcr.io/etcd:3.2.24
docker tag coredns/coredns:1.2.6 k8s.gcr.io/coredns:1.2.6



docker rmi mirrorgooglecontainers/kube-apiserver:v1.13.0
docker rmi mirrorgooglecontainers/kube-controller-manager:v1.13.0
docker rmi mirrorgooglecontainers/kube-scheduler:v1.13.0
docker rmi mirrorgooglecontainers/kube-proxy:v1.13.0
docker rmi mirrorgooglecontainers/pause:3.1
docker rmi mirrorgooglecontainers/etcd:3.2.24
docker rmi coredns/coredns:1.2.6

```


```
Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"
```