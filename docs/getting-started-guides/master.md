# 部署Kubernetes Master节点

* 创建‘play’，指定执行task的目标机和对象,master对象为master主机组
```ssh
- hosts: masters
  roles:
    - masters
```
* 创建任务列表

```ssh
# 在master节点上，创建可执行文件存放路径
- name: create /opt/bin directory
  file: path=/opt/bin state=directory mode=0755

#  
# copy flanneld, kubelet, kube-dns, kubectl可执行文件到目标机存放路径
# 其中变量，k8s_version，flanneld_version等在default/main.yml文件中声明和定义
#
- name: copy flanneld
  copy: src=flanneld-{{ flanneld_version }} dest=/opt/bin/flanneld mode=0755
- name: copy flanneld
  copy: src=kubelet-{{ k8s_version }} dest=/opt/bin/kubelet mode=0755
- name: copy kube-dns
  copy: src=kube-dns-{{ k8s_version }} dest=/opt/bin/kube-dns mode=0755
- name: copy kubectl
  copy: src=kubectl-{{ k8s_version }} dest=/opt/bin/kubectl mode=0755
- name: copy kubectl
  copy: src=kubectl-{{ k8s_version }} dest=/usr/local/bin/kubectl mode=0755
  
# 检测kube-proxy以及kube-kubelet 8080端口是否启用
- name: copy wupiao
  copy: src=wupiao dest=/opt/bin/wupiao mode=0755
```

```ssh
# 根据模板，创建service文件
- name: write flanneld.service
  template: src=flanneld.service.j2 dest=/etc/systemd/system/flanneld.service
- name: write docker.service
  template: src=docker.service.j2 dest=/etc/systemd/system/docker.service
- name: write kube-kubelet.service file
  template: src=kube-kubelet.service.j2 dest=/etc/systemd/system/kube-kubelet.service
- name: write kube-dns.service file
  template: src=kube-dns.service.j2 dest=/etc/systemd/system/kube-dns.service

```

```ssh
# 创建yaml文件，kubernetes会依据以下yaml文件自动创建系统组件
- name: create /etc/kubernetes/manifests directory
  file: path=/etc/kubernetes/manifests state=directory mode=0755
- name: write kube-apiserver.yaml file
  template: src=kube-apiserver.yaml.j2 dest=/etc/kubernetes/manifests/kube-apiserver.yaml
- name: write kube-proxy.yaml file
  template: src=kube-proxy.yaml.j2 dest=/etc/kubernetes/manifests/kube-proxy.yaml
- name: write kube-scheduler.yaml file
  template: src=kube-scheduler.yaml.j2 dest=/etc/kubernetes/manifests/kube-scheduler.yaml
- name: write kube-controller-manager.yaml file
  template: src=kube-controller-manager.yaml.j2 dest=/etc/kubernetes/manifests/kube-controller-manager.yaml
```
```ssh
# 重新加载service文件，并启动服务
- name: daemon reload
  shell: systemctl daemon-reload
- name: start flanneld
  service: name=flanneld state=restarted enabled=yes
- name: start docker
  service: name=docker state=restarted enabled=yes
- name: start kube-kubelet
  service: name=kube-kubelet state=restarted enabled=yes
- name: start kube-dns
  service: name=kube-dns state=restarted enabled=yes

```

```ssh
# 执行playbook，创建master节点
ansible-playbook master.yml -i inventory
```




