[TOC]

## 1. 安装

> 环境 MacOS

```
sudo pip install ansible
```

## 2. 配置

> 创建hosts文件

```
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

```
jumper ansible_ssh_user=ubuntu ansible_ssh_host=***.***.***.230
```





### 2.2 测试连接主机

> 添加hosts后，就通过ping module测试是否能连接相应的主机
>
> 如果主机默认用户不是root，则可通过 -u 指定用户，如sudo ansible all -m ping  -k -u username

```
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





