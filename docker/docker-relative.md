## 阿里云镜像仓库

registry.cn-shenzhen.com
registry.cn-hongkong.aliyuncs.com/tzm-k8s-gcr-io



## 阿里云加速

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://aac6fx4m.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker