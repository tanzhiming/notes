## docker 安装

1. 卸载老版本（如果有安装）

   ```shell
   $ sudo yum remove docker \
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

2. 安装所需软件

   ```shell
   $ sudo yum install -y yum-utils \
     device-mapper-persistent-data \
     lvm2
   ```

3. 设置yum源

   ```shell
   $ sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```
   > 国内环境网络慢的情况下，可使用aliyun的镜像源
   >
   > ```shell
   > $ sudo yum-config-manager \
   > --add-repo \
   > http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   > ```

4. 安装docker-ce

   ```shell
   $ sudo yum install -y docker-ce
   ```

5. 设置开机启动

   ```shell
   $ systemctl enable docker
   ```


6. 启动docker

   ```shell
   $ systemctl start docker
   ```

7. 阿里云镜像加速（可选，加速镜像下载）

    ```shell
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
    "registry-mirrors": ["https://aac6fx4m.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```


### docker compose 安装

```shell
$ curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
```