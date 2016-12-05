> 所有机器均为CentOS7操作系统
* 准备主机
| 主机  | 角色 | IP |
| :---: | :---: | :---: |
| node01 | master | 172.16.xx.2 |
| node02 | etcd | 172.16.xx.3 |
| node03 | registry | 172.16.xx.4 |
| node04 | minon1 | 172.16.xx.5 |
| node05 | minon2 | 172.16.xx.6 |
| node06 | ansible | 172.16.xx.7 |

* 建立ansible与各节点的连接，设置ssh-keygen无秘钥登陆
1. 修改/etc/hosts文件
```ssh
$ cat /etc/hosts
172.16.xx.2 master
172.16.xx.3 etcd
172.16.xx.5 minon1
...
..
``
2. 用ssh-keygen创建公钥
```ssh

[root@Server1 ~]# ssh-keygen -t rsa  
Generating public/private rsa key pair.  
Enter file in which to save the key(/root/.ssh/id_rsa):  
Created directory '/root/.ssh'.  
Enter passphrase (empty for no passphrase):  
Enter same passphrase again:  
Your identification has been saved in/root/.ssh/id_rsa.  
Your public key has been saved in/root/.ssh/id_rsa.pub.  
The key fingerprint is:  
7b:aa:08:a0:99:fc:d9:cc:d8:2e:4b:1a:c0:6b:da:e4root@Server1  
The key's randomart image is:  
+--[ RSA 2048]----+  
| |  
| |  
| |  
|. |  
|o. S |  
|++. . |  
|+=o. . . |  
|o+=oB. o |  
|..E==*... |  
+-----------------+
```
> 输入后，会提示创建.ssh/id_rsa、id_rsa.pub的文件，其中第一个为密钥，第二个为公钥。过程中会要求输入密码，为了ssh访问过程无须密码，可以直接回车 。

3. 将公钥复制到被管理机器Server2和Server3下的.ssh目录下

```ssh
[root@ansible .ssh]#  scp id_rsa.pub root@172.16.xx.xx:~/.ssh/ 
```
4. 到master和minions目录下执行下面的命令
```ssh
cat id_dsa.pub >> ~/.ssh/authorized_keys 
```

5. 验证使用SSH IP地址的方式无密码访问
```ssh
$ ssh master
$ ssh minion01
```