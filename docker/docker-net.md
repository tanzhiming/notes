# Docker 修改默认网段

## 删除原有配置

```shell
sudo service docker stop
sudo ip link set dev docker0 down
sudo brctl delbr docker0
sudo iptables -t nat -F POSTROUTING
```

## 创建新的网桥
```shell
sudo brctl addbr docker0
sudo ip addr add 172.17.0.1/24 dev docker0
sudo ip link set dev docker0 up
```

## 配置Docker文件

vi /etc/docker/daemon.json

```shell
{"registry-mirrors": ["http://d552c9b5.m.daocloud.io"], "bip": "172.17.0.1/24"}
```

