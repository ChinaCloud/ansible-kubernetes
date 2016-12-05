# 部署Kubernetes内部认证配置

* 首先创建‘play’，指定执行task的目标机和对象
```ssh
- hosts: masters
  roles:
    - k8s-ssh
- hosts: minions
  roles:
    - k8s-ssh
```
* 创建task列表
```ssh
- name: create /tmp/ssl directory
  local_action: file path=/tmp/ssl state=directory mode=0755
  sudo: false
  run_once: true

- name: Generate root CA
  local_action: script init-ssl-ca.sh /tmp/ssl
  sudo: false
  run_once: true

- name: Generate admin key/cert
  local_action: script init-ssl.sh /tmp/ssl admin kube-admin
  sudo: false
  run_once: true

- name: provision masters ssl
  local_action: script init-ssl.sh /tmp/ssl apiserver kube-apiserver-{{ hostvars[item]['ansible_default_ipv4']['address'] }} {% for host in groups['masters'] %}IP.{{ loop.index }}={{ hostvars[host]['ansible_default_ipv4']['address'] }}{% if not loop.last %},{% else %},IP.{{ loop.index+1 }}={{ k8s_service_ip }}{% endif %}{% endfor %}
  sudo: false
  with_items: "{{ groups['masters'] }}"
  run_once: true

- name: provision works ssl
  local_action: script init-ssl.sh /tmp/ssl worker-{{ ansible_default_ipv4.address }} kube-worker-{{ ansible_default_ipv4.address }} IP.1={{ ansible_default_ipv4.address }}
  sudo: false
```

