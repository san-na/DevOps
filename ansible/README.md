## 1. 安装

> 环境 MacOS

```bash
sudo pip install ansible
```

## 2. 配置

> 创建hosts文件

```bash
sudo mkdir -p /etc/ansible
sudo vim /etc/ansible/hosts
```



### 2.1 添加主机

> 在hosts中加入相应的主机，也可以根据自己实际情况创建相应组,比如我的
>
> 另外，如果修改了ssh默认端口，则在相应的ip后面加上端口

```
[***]
120.***.8.**:7788

[***]
***.***.***.230


[local]
192.168.1.180
```

> 还可以通过定义别名的方式将服务器信息写入hosts文件，如下
> 其中jumper为该服务器别名，可根据自己实际情况修改为其它任意字符串

```
jumper ansible_ssh_user=ubuntu ansible_ssh_host=***.***.***.230
```
> 相关的参数还有

```bash
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




### 2.2 测试连接主机

> 添加hosts后，就通过ping module测试是否能连接相应的主机
>
> 如果主机默认用户不是root，则可通过 -u 指定用户，如sudo ansible all -m ping  -k -u username

```bash
➜  ~ sudo ansible all -m ping  -k
SSH password:
***.***.***.230 | UNREACHABLE! => {
    "changed": false,
    "msg": "('Bad authentication type', [u'publickey']) (allowed_types=[u'publickey'])",
    "unreachable": true
}
120.***.8.** | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.1.180 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```



### 2.3 ansible-doc

> 使用过程中可以通过ansible-doc 查看一些模块的介绍

```
➜  ~ ansible-doc -l | wc -l
     773
➜  ~ ansible-doc command
```

### 2.4 ansible基本功能-copy文件

> 实现将某文件copy到所有机器上，以nihao.py为例

```bash
➜  ~ sudo ansible all -m copy -a "src=nihao.py dest=/tmp/ " -k
SSH password:
***.***.***.** | SUCCESS => {
    "changed": true,
    "checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
    "dest": "/tmp/nihao.py",
    "gid": 0,
    "group": "root",
    "md5sum": "d41d8cd98f00b204e9800998ecf8427e",
    "mode": "0644",
    "owner": "root",
    "size": 0,
    "src": "/root/.ansible/tmp/ansible-tmp-1489661516.77-128012169072532/source",
    "state": "file",
    "uid": 0
}
192.168.1.180 | SUCCESS => {
    "changed": true,
    "checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
    "dest": "/tmp/nihao.py",
    "gid": 0,
    "group": "root",
    "md5sum": "d41d8cd98f00b204e9800998ecf8427e",
    "mode": "0644",
    "owner": "root",
    "size": 0,
    "src": "/root/.ansible/tmp/ansible-tmp-1489661516.91-185355159665551/source",
    "state": "file",
    "uid": 0
}
backup | SUCCESS => {
    "changed": true,
    "checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
    "dest": "/tmp/nihao.py",
    "gid": 500,
    "group": "ubuntu",
    "md5sum": "d41d8cd98f00b204e9800998ecf8427e",
    "mode": "0664",
    "owner": "ubuntu",
    "size": 0,
    "src": "/home/ubuntu/.ansible/tmp/ansible-tmp-1489661517.06-86408822620129/source",
    "state": "file",
    "uid": 500
}
```
> 然后查看是否在服务器上

```bash
➜  ~ sudo ansible all -m command -a "ls -l /tmp/nihao.py" -k
SSH password:
***.***.**.*** | SUCCESS | rc=0 >>
-rw-r--r-- 1 root root 0 3月  16 18:51 /tmp/nihao.py

120.132.8.73 | SUCCESS | rc=0 >>
-rw-r--r-- 1 root root 0 Mar 16 00:51 /tmp/nihao.py

backup | SUCCESS | rc=0 >>
-rw-rw-r-- 1 ubuntu ubuntu 0 Mar 16 18:51 /tmp/nihao.py
```
