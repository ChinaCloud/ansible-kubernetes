# 安装registry
之前我们已经简单介绍过playbook,和inventory的用法，不在赘述。以下我们直接介绍registry的创建过程：
* 首先创建play，指定执行task的目标机和对象
```ssh
---
- hosts: registry
  roles:
    - registry
```

* 创建task列表
```ssh
---
- fail: msg="Bailing out. this play requires 'registry_host'"
  when: registry_host is not defined
- fail: msg="Bailing out. this play requires 'registry_port'"
  when: registry_port is not defined
```
> 这里使用了条件语句when，满足条件后将执行语句，如：fail

```ssh
- name: cp docker service
  template: src=docker.service.j2 dest=/etc/systemd/system/docker.service
- name: daemon reload
  shell: systemctl daemon-reload
- name: start docker.service
  service: name=docker state=started enabled=yes
```
> 这里主要是重写docker.service以设置insecure-registry, 然后重启docker


```ssh
- name: upload registry image
  copy: src=registry-2.tar dest=/tmp/registry-2.tar

- name: upload pause image
  copy: src=gcr.io~google_containers~pause-amd64~3.0.tar dest=/tmp/gcr.io~google_containers~pause-amd64~3.0.tar
- name: upload hyperkube image
  copy: src=gcr.io-google_containers-hyperkube-v1.3.7.tar dest=/tmp/gcr.io-google_containers-hyperkube-v1.3.7.tar
- name: load registry image
  shell: docker load -i /tmp/registry-2.tar
- name: load pause image
  shell: docker load -i /tmp/gcr.io~google_containers~pause-amd64~3.0.tar
- name: load hyperkube image
  shell: docker load -i /tmp/gcr.io-google_containers-hyperkube-v1.3.7.tar
```
> 上传并加载必要的镜像，如registry，还有kubernetes环境启动所必须的pause, hyperkube镜像

```ssh
- name: copy pip-7.1.2.tar.gz
  copy: src=pip-7.1.2.tar.gz dest=/tmp/pip-7.1.2.tar.gz
- name: install pip
  shell: easy_install /tmp/pip-7.1.2.tar.gz
- name: copy docker-py
  copy: src=docker-py dest=/tmp/
- name: install docker-py
  shell: pip install -U --no-index --find-links=file:///tmp/docker-py docker-py
```
> 安装依赖包

```ssh
- name: create docker registry
  docker:
  # 设置镜像容器名称
    name: registry
    image: registry:2
  # 重启规则
    restart_policy: always
    privileged: yes
  # 设置端口
    ports:
      - "0.0.0.0:{{ registry_port }}:5000"
    # 设置容器映射到外部设备路径
    volumes:
      - "{{ volume_path }}:/var/lib/registry"
    env:
      REGISTRY_DELETE_ENABLED: true
  ignore_errors: true
```
> 设置registry容器启动参数

```ssh

# 执行playbook
ansible-playbook registry.yml -i inventory
```

