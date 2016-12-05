# 部署ETCD集群

* 首先创建play，指定执行task的目标机和对象
```ssh
- hosts: etcd
  roles:
    - etcd
```
* 创建任务列表

```ssh
# 创建etcd可执行文件的存放路径
- name: create /opt/bin directory
  file: path=/opt/bin state=directory mode=0755
  
# 上传etcdctl可执行文件到目标路径
- name: copy etcd
  copy: src=etcd-{{ etcd_version }} dest=/opt/bin/etcd mode=0755
- name: copy etcdctl
  copy: src=etcdctl-{{ etcd_version }} dest=/opt/bin/etcdctl mode=0755
# 创建并设置etcd.service 文件
- name: wirte etcd service
  template: src=etcd.service.j2 dest=/etc/systemd/system/etcd.service

# 启动etcd集群
- name: daemon reload
  shell: systemctl daemon-reload

- name: start etcd
  service: name=etcd state=restarted enabled=yes
```

```ssh
# 执行playbook，创建etcd集群
ansible-playbook etcd.yml -i inventory
```
