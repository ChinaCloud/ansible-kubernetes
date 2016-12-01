# Inventory文件
###### Topics
* [主机与组](#主机与组)
* [主机变量](#主机变量)
* [组的变量](#组的变量)
* [Inventory参数的说明](#Inventory参数的说明)

### 主机与组

###### 主机

假设你有一些静态IP地址,希望设置一些别名,但不是在系统的 host 文件中设置,又或者你是通过隧道在连接,那么可以设置如下:

```sh
# [name]  ansible_host=[IP] ansible=[username] ansible_ssh_pass=[password]
registry ansible_host=172.16.xx.12 ansible_user=root ansible_ssh_pass=centos
dns ansible_host=172.16.xx.13 ansible_user=root
master ansible_host=172.16.80.xx.14 ansible_user=root
minion01 ansible_host=172.16.xx.15 ansible_user=root
minion02 ansible_host=172.16.xx.16 ansible_user=root
```
如果有主机的SSH端口不是标准的22端口,可在主机名之后加上端口号,用冒号分隔.SSH 配置文件中列出的端口号不会在 paramiko 连接中使用,会在 openssh 连接中使用.
```ssh
test01 ansible_ssh_port=5555 ansible_ssh_host=192.168.xx.xx
```
在这个例子中,通过 “test01” 别名,会连接 192.168.xx.xx:5555.记住,这是通过 inventory 文件的特性功能设置的变量

##### 组
```ssh
[machines]
master
minion01
minion02
```

```ssh
[etcd]
master
minion01
minion02
```
方括号[]中是组名,用于对系统进行分类,便于对不同系统进行个别的管理.
一个系统可以属于不同的组,比如一台服务器可以同时属于 machines 和 etcd.这时属于两个组的变量都可以为这台主机所用

### 主机变量

前面已经提到过,分配变量给主机很容易做到,这些变量定义后可在 playbooks 中使用:
```ssh
master ansible_host=172.16.80.xx.14 ansible_user=root
minion01 ansible_host=172.16.xx.15 ansible_user=root
minion02 ansible_host=172.16.xx.16 ansible_user=root
```
### 组的变量
可以定义属于整个组的变量:
```ssh
[registry]
registry

[registry:vars]
registry_host=172.16.xx.12
registry_port=80
```
### Inventory参数的说明

```ssh
ansible_ssh_host
      将要连接的远程主机名.与你想要设定的主机的别名不同的话,可通过此变量设置.

ansible_ssh_port
      ssh端口号.如果不是默认的端口号,通过此变量设置.

ansible_ssh_user
      默认的 ssh 用户名

ansible_ssh_pass
      ssh 密码(这种方式并不安全,我们强烈建议使用 --ask-pass 或 SSH 密钥)

ansible_sudo_pass
      sudo 密码(这种方式并不安全,我们强烈建议使用 --ask-sudo-pass)

ansible_sudo_exe (new in version 1.8)
      sudo 命令路径(适用于1.8及以上版本)

ansible_connection
      与主机的连接类型.比如:local, ssh 或者 paramiko. Ansible 1.2 以前默认使用 paramiko.1.2 以后默认使用 'smart','smart' 方式会根据是否支持 ControlPersist, 来判断'ssh' 方式是否可行.

ansible_ssh_private_key_file
      ssh 使用的私钥文件.适用于有多个密钥,而你不想使用 SSH 代理的情况.

ansible_shell_type
      目标系统的shell类型.默认情况下,命令的执行使用 'sh' 语法,可设置为 'csh' 或 'fish'.

ansible_python_interpreter
      目标主机的 python 路径.适用于的情况: 系统中有多个 Python, 或者命令路径不是"/usr/bin/python",比如  \*BSD, 或者 /usr/bin/python
      不是 2.X 版本的 Python.我们不使用 "/usr/bin/env" 机制,因为这要求远程用户的路径设置正确,且要求 "python" 可执行程序名不可为 python以外的名字(实际有可能名为python26).

      与 ansible_python_interpreter 的工作方式相同,可设定如 ruby 或 perl 的路径....
```