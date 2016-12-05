# 部署Kubernetes内部认证配置

> 利用ansible部署k8s的内部认证，比较简单的是通过CSV的认证方式。

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
# 首先创建api server的基本认证文件，并且生成CA和api server的证书
# 此步骤将由执行脚本init-ssl-ca.sh和init-ssl.sh共同完成
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
> 上面的命令会生成若干证书相关文件，作用如下：
> ca.key：自己生成的CA的私钥，用于模拟一个CA
> ca.crt：用自己的私钥自签名的CA证书
> server.key：api server的私钥，用于配置api server的https
> server.csr：api server的证书请求文件，用于请求api server的证书
> server.crt：用自己模拟的CA签发的api server的证书，用于配置api server的https

> 接下来，把证书文件拷贝到每个节点，包括master和minion节点。
> 然后创建配置组件.

```ssh
- name: create /etc/kubernetes directory
  file: path=/etc/kubernetes state=directory mode=0755

- name: copy ssl directory
  copy: src=/tmp/ssl dest=/etc/kubernetes

- name: write worker-kubeconfig.yaml
  template: src=worker-kubeconfig.yaml.j2 dest=/etc/kubernetes/worker-kubeconfig.yaml
```
> 我们还会在创建master和minion节点时候进行配置
