---
title: pod 的创建与使用
mathjax: true
date: 2025-07-27 23:36:05
tags: k8s
category: k8s
---

pod 的创建与使用

<!--more-->

## 创建pod

pod是k8s的最小调度和管理单元，从概念上pod是docker的集合。为了创建pod，先编写一下yaml。

```yaml
# nginx
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx-pod
      image: nginx:stable-perl
```

通过命令`kubectl apply -f nginx.yaml`便可以创建一个运行nginx的pod。如果发现镜像无法拉取，可以通过`minikube ssh`登录虚拟机操作docker。

```sh
gboom@gboom-PC:~/Desktop/blog/source/_posts/k8s$ kubectl get pod
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          17m
```

可以看到pod创建成功。登录虚拟机可以看到运行了nginx容器。

```sh
docker@minikube:~$ docker ps
CONTAINER ID   IMAGE                        COMMAND                  CREATED          STATUS          PORTS     NAMES
5feb5dd011f2   nginx                        "/docker-entrypoint.…"   14 minutes ago   Up 14 minutes             k8s_nginx-pod_nginx-pod_default_ca05b187-971c-4819-a56b-d07c6408c504_0
```

## 端口映射

为了能够访问nginx，我们需要进行端口映射。

```sh
gboom@gboom-PC:~/Desktop/blog/source/_posts/k8s$ kubectl port-forward nginx-pod  8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

通过浏览器访问127.0.0.1：8080，得到

<img src="/home/gboom/.config/Typora/typora-user-images/image-20250727235100834.png" alt="image-20250727235100834" style="zoom:50%;" />



此时再登录虚拟机，发现docker ps还是看不到端口。后面要看下k8s的网络是怎么样的。

```sh
gboom@gboom-PC:~$ minikube ssh
docker@minikube:~$ docker ps
CONTAINER ID   IMAGE                        COMMAND                  CREATED          STATUS          PORTS     NAMES
5feb5dd011f2   nginx                        "/docker-entrypoint.…"   19 minutes ago   Up 19 minutes             k8s_nginx-pod_nginx-pod_default_ca05b187-971c-4819-a56b-d07c6408c504_0

```

