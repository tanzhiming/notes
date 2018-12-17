## 设置阿里yum源

1. 备份原来的yum源

```shell
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

2. 设置阿里yum源

```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

3. 清理并生成新的缓存

```shell
yum clean all
yum makecache
```

4. 更新yum库

```
yum update
```