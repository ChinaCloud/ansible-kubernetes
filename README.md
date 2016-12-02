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
* Step 3: 安装容器镜像仓库
* Step 4: 部署Etcd集群
* Step 5: 部署Kubernetes内部认证配置
* Step 6: 部署Kubernetes Master节点
* Step 7: 部署Kubernetes Minion节点

## 技巧提示

## 已知问题

## 贡献者

> 按加入项目时间先后顺序排序

* Wenbao Xu, xuwenbao@chinacloud.com.cn
* Tao Yu, yutao@chinacloud.com.cn

## 开源协议

ansible-k8s基于Apache License Version 2.0发布(ansible-k8s is released under the Apache License Version 2.0).
