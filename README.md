# Ansible离线部署Kubernetes集群

> 目前仅在centos7系统上测试通过.

通过Ansible部署Kubernetes集群至物理机/虚拟机. 已内置Kubernetes运行所需的可执行文件, 如: Docker安装文件、Etcd可执行文件、Flannel可执行文件等, 部署过程全自动, 并且无需访问因特网.

> 由于一些内置文件比较大, 您需要安装Git LFS拓展, 以下载本仓库, 详见: https://git-lfs.github.com

默认部署软件版本:

| 软件  | 版本 |
| :---: | :---: |
| Docker Engine | v1.12.1 |
| Docker Registry | v2.0 |
| Etcd | v2.3.7 |
| Flannel | v0.5.3 |
| Kubernetes | v1.3.7 |

您也可以下载其他版本的可运行文件放置到指定目录中, 通过Ansible变量指定部署其他Kubernetes或依赖软件版本.

## 概览

* [准备](#准备)
* [快速开始](#快速开始)
* [技巧提示](#技巧提示)
* [已知问题](#已知问题)
* [贡献者](#贡献者)
* [开源协议](#开源协议)

## 准备

* Step 1: [准备环境](docs/ready-for-install/ENVIRONMENT.md)
* Step 2: [安装Ansible](docs/ready-for-install/ANSIBLE_INSTALLATION.md)


## 快速开始

* Step 1: [创建Inventory文件](docs/getting-started-guides/inventory.md)
* Step 2: [关闭防火墙, 安装Docker](docs/getting-started-guides/installdocker.md)
* Step 3: [安装容器镜像仓库](docs/getting-started-guides/installregistry.md)
* Step 4: [部署Etcd集群](docs/getting-started-guides/etcdcluster.md)
* Step 5: [部署Kubernetes内部认证配置](docs/getting-started-guides/k8s-ssh.md)
* Step 6: [部署Kubernetes Master节点](docs/getting-started-guides/master.md)
* Step 7: [部署Kubernetes Minion节点](docs/getting-started-guides/minions.md)

## 技巧提示

* 除了以上可以单步执行部署步骤以外，还可以一键部署，如下：
```ssh
$ cat ./all-in-one.yml

# Step 1, 关闭防火墙, 安装Docker
- include: base.yml
# Step 2, 安装容器镜像仓库
- include: registry.yml
# Step 3, 部署Etcd集群
- include: etcd.yml
# Step 4, 部署Kubernetes ssh安全配置
- include: k8s-ssh.yml
# Step 5, 部署Kubernetes Master节点
- include: masters.yml
# Step 6, 部署Kubernetes Minion节点
- include: minions.yml
```
> 以上步骤将会从上到下依次执行，你也可以根据需要在此文件中修改

## 已知问题

* 配置k8s-ssh认证服务的时候，执行脚init-ca-ssl.sh和init-ssl.sh可能会报错， 可能的原因:

	1) 因为上传的时候造成了脚本格式错乱，因此，你可以利用dos2unix工具格式化脚本
	
	2) ssh登陆不上，重新验证ssh-keygen无秘钥登陆
	
* 有些虚拟机可能没有防火墙配置，此时你应该注释掉base中对防火墙的设置.
* 最好使用docker 1.11版本，因为在docker 1.12中有时候会出现pause起不来的现象


## 贡献者

> 按加入项目时间先后顺序排序

* Wenbao Xu, xuwenbao@chinacloud.com.cn
* Tao Yu, yutao@chinacloud.com.cn

## 开源协议

ansible-k8s基于Apache License Version 2.0发布(ansible-k8s is released under the Apache License Version 2.0).
