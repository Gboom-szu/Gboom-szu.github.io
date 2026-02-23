---
title: hexo搭建博客
mathjax: true
date: 2025-07-20 23:15:48
tags: 杂项
category: 杂项

---

基于hexo+github搭建个人博客。

hexo的安装与使用参考[hexo官网](https://hexo.io/zh-cn/docs/commands)

<!--more-->

## node js安装

在Ubuntu安装参考官网的方法[安装Nodejis](https://nodejs.org/en/download)

```bash
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"
# Download and install Node.js:
nvm install 22
```

安装成功后更换npm镜像源

```bash
npm config set registry https://registry.npmmirrot.com/
```

## hexo 安装

```bash
 npm install hexo-cli -g
```

## hexo 使用

初始化项目

```bash
hexo init
```

新建博客

```bash
hexo new <title>
```

还可以通过以下命令创建博客时指定子路径，方便分类管理.02-pod就是文件名

```shell
hexo n post  -p k8s/02-pod
```



启动项目，可以在本地访问博客

```bash
hexo start
```

部署项目

修改_config.yml

```bash
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: git@github.com:Gboom-szu/Gboom-szu.github.io.git
  branch: main
```

发布博客，自动部署到GitHub上，通过https://gboom-szu.github.io/就可以访问博客啦

```bash
hexo d
```

