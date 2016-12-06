# Ansible Installation


ansible是新出现的自动化运维工具，基于Python开发，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。
ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。主要包括：
- 接插件connection plugins：负责和被监控端实现通信；
- ost inventory：指定操作的主机，是一个配置文件里面定义监控的主机；
- 各种模块核心模块、command模块、自定义模块；
- 借助于插件完成记录日志邮件等功能；
- playbook：剧本执行多个任务时，非必需可以让节点一次性运行多个任务。 

详细可以参考：http://www.ansible.com.cn/docs/intro.html

### 安装ansible对管理主机的要求：
- 安装了Python 2.6 或 Python 2.7 (windows系统不可以做控制主机),都可以运行Ansible.这里安装的是python2.7
- 主机的系统可以是 Red Hat, Debian, CentOS, OS X, BSD的各种版本,这里只用的是centos7

### 安装ansible对托管节点的要求：
- 安装python2.6或者Python2.7
- 系统要求同上
- 能够ssh通信

### 安装ansible

- 从源码安装

安装ansible之前，以下的Python模块也需要安装
```sh
$ sudo pip install paramiko PyYAML Jinja2 httplib2 six
```
你也可以直接安装这些依赖包：
```sh
$ cd  packages
$ rpm -ivh *.rpm
```
安装ansible
```sh
$ git clone git://github.com/ansible/ansible.git --recursive
# or get the ansibel packages from directory "packages"
$ tar -xvzf  ansible.tar.gz
$ cd ./ansible
$ python setup.py install
```

### 通过Yum安装最新发布版本
通过Yum安装RPMs适用于 EPEL 6, 7, 以及仍在支持中的Fedora发行版.

托管节点的操作系统版本可以是更早的版本(如 EL5), 但必须安装 Python 2.4 或更高版本的Python.

Fedora 用户可直接安装Ansible, 但RHEL或CentOS用户,需要 配置 EPEL

配置epel源：
```sh
$ wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
$ rpm -ivh epel-release-7-5.noarch.rpm
```
安装ansible
```sh
# install the epel-release RPM if needed on CentOS, RHEL, or Scientific Linux
$ sudo yum install ansible
```
参考来源：http://www.ansible.com.cn/docs/intro_installation.html



