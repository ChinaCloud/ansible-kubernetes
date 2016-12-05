# playbook

在利用ansible部署集群之前，先简单介绍一下playbook：

简单来说,playbooks是一种简单的配置管理系统与多机器部署系统的基础.与现有的其他系统有不同之处,且非常适合于复杂应用的部署.
   Playbooks 可用于声明配置,更强大的地方在于,在 playbooks 中可以编排有序的执行过程,甚至于做到在多组机器间,来回有序的执行特别指定的步骤.并且可以同步或异步的发起任务.
   playbook我们以后会单独开一个章节来介绍，这里具体你可以参考：http://www.ansible.com.cn/docs/playbooks_intro.html#about-playbooks
   
Playbooks 的格式是YAML. playbook 由一个或多个 ‘plays’ 组成.它的内容是一个以 ‘plays’ 为元素的列表.在 play 之中,一组机器被映射为定义好的角色.

```ssh
# base.yml
----
- hosts: machines
  roles:
    - base
```
> Roles 的概念来自于这样的想法：通过 include 包含文件并将它们组合在一起，组织成一个简洁、可重用的抽象对象。这种方式可使你将注意力更多地放在大局上，只有在需要时才去深入了解细节。以下roles为base所include的内容(包含了task列表和task执行时所依赖的文件):
```ssh
--|roles
-----base
------files
--------docker-engine-1.12.1-1.el7.centos.x86_64.rpm
------tasks
--------main.yml
```

在基本层次的应用中,一个任务是一个对 ansible 模块的调用. ‘plays’ 好似音符,playbook 好似由 ‘plays’ 构成的曲谱,通过 playbook,可以编排步骤进行多机器的部署,比如在 [machines] 组的所有机器上运行一定的步骤, 然后在 [dns]组运行一些步骤

上一章节[创建Inventory文件](./inventory.md)我们为playbook 中的每一个 play创建好了目标机或者组,下面我们将以这些用户身份去完成要执行的步骤（called tasks）.在 ansible 中,play 的内容,被称为 tasks,即任务.这里我们为每一个play创建了一个task列表，一个 task 在其所对应的所有主机上执行完毕之后,下一个 task 才会执行.有一点需要明白的是（很重要）,在一个 play 之中,所有 hosts 会获取相同的任务指令,这是 play 的一个目的所在,也就是将一组选出的 hosts 映射到 task, example:
```ssh
$ cat ./roles/base/tasks/main.yml

- name: stop firewalld
  service: name=firewalld state=stopped enabled=no

- name: create /tmp/docker-install directory
  file: path=/tmp/docker-install state=directory mode=0755
  ......
  ....
- name: copy docker-engine-1.12.1-1.el7.centos.x86_64.rpm
  copy: src=docker-engine-1.12.1-1.el7.centos.x86_64.rpm dest=/tmp/docker-install/docker-engine-1.12.1-1.el7.centos.x86_64.rpm
  ......
  ....
- name: install docker engine
  yum: name=/tmp/docker-install/docker-engine-1.12.1-1.el7.centos.x86_64.rpm state=present

```
> 以上task 列表将按照先后顺序在之前所指定的目标机上执行，即[hosts]所包含的主机

下面将详细介绍task列表功能：

### 通过playbook关闭防火墙


* 创建playbook中的“play”, 即base.yml,然后指定主机组执行动作
---
```ssh
- hosts: machines
  roles:
    - base
```
> 这里指定machines主机组执行动作,hosts 行的内容是一个或多个组;roles是指定主机或者主机组所执行的task来源

* 定义执行的动作，即task
```ssh
- name: stop firewalld
  service: name=firewalld state=stopped enabled=no
```
>上面是一种基本的 task 的定义,service moudle 使用 key=value 格式的参数,这也是大多数 module 使用的参数格式。每一个 task 必须有一个名称 name,这样在运行 playbook 时,从其输出的任务执行信息中可以很好的辨别出是属于哪一个 task 的. 如果没有定义 name,‘action’ 的值将会用作输出信息中标记特定的 task.

* 执行playbook ansible_playbook base.yml -i inventory

### 通过playbook安装docker

* 首先我们创建一个目录来存放docker源文件
```ssh
- name: create /tmp/docker-install directory
  file: path=/tmp/docker-install state=directory mode=0755
```
> 上面是创建一个名字为/tmp/docker-install的目录，权限为0755

* 第二步，我们将拷贝源文件到指定目录
```ssh
- name: copy docker-engine-1.12.1-1.el7.centos.x86_64.rpm
  copy: src=docker-engine-1.12.1-1.el7.centos.x86_64.rpm dest=/tmp/docker-install/docker-engine-1.12.1-1.el7.centos.x86_64.rpm
```
> 执行动作，copy；拷贝源，src；目的地，dest

* 第三步，安装docker-engine

```ssh
- name: install docker engine
  yum: name=/tmp/docker-install/docker-engine-1.12.1-1.el7.centos.x86_64.rpm state=present
```
> 执行动作，yum install *.rpm

* 创建用户
```ssh
- name: add user developer, password is "developer"
  user: name=developer shell=/bin/bash groups=docker password=xxxxxxxxxxxx
```
> 这里有点区别的是采用了shell moudule，playbook将在指定主机执行shell命令。

```ssh
# 执行playbook
ansible-playbook base.yml -i inventory
```
