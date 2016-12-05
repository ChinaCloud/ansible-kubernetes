# 部署Kubernetes Minion节点

* 创建play，指定执行task的目标机和对象
```ssh
- hosts: minions
  roles:
    - minions
```

* 创建task列表

```ssh
# 步骤与创建master节点差不多，只是创建minion的时候需要启动kube-proxy服务
- name: copy kube-proxy
  copy: src=kube-proxy-{{ k8s_version }} dest=/opt/bin/kube-proxy mode=0755
```

```ssh
# 执行playbook，创建minions节点
ansible-playbook master.yml -i inventory
```