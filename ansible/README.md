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
