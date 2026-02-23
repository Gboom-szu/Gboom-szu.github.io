---
title: minkube安装
mathjax: true
date: 2025-07-20 23:47:20
tags: k8s
category: k8s
---

学习k8s，安装minekube环境

<!--more-->

## 安装docker

参考：https://www.zhangjc.com/2025/05/30/Deepin-23-10%E5%AE%89%E8%A3%85Docker/

1.安装依赖包

```
sudo apt install -y ca-certificates curl gnupg lsb-release
```

2.添加 Docker 官方 GPG 密钥

```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

3.添加 Docker 源

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4.安装docker

```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

5.配置用户权限

```
sudo usermod -aG docker $USER
newgrp docker  # 刷新用户组
```

6.配置镜像加速器（国内用户必选）

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
	"https://docker.xuanyuan.me"
  ]
}
EOF

sudo systemctl daemon-reload 
sudo systemctl restart docker
```

## 安装minikube

使用minikube搭建本地集群，[官方安装文档](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download)

```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

## 启动minikube

```
minikube start
```

首次执行会首先下载一些依赖文件。

minikebe 中docker设置

```sh
# 登录minikube容器
minikube ssh
# 和之前一样设置docker镜像地址
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
	"https://docker.xuanyuan.me"
  ]
}
EOF

sudo systemctl daemon-reload 
sudo systemctl restart docker
```

