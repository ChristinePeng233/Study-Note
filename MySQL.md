# MySQL

## 一.MySQL安装

### 1.1卸载内置环境

- 切换成超级用户
- 查看mysql进程：`ps ajx | grep mysql`
- 如果预置了mysql，则用`systemctl stop mysqld`停用MySQL
- 查看mysql的安装包：`rpm -qa | grep mysql`
- 卸载：`rpm -qa | grep mysql | xargs yum  -y remove`

### 1.2配置MySQL yum源

- 获取mysql官方yum源 [https://repo.mysql.com/](https://repo.mysql.com/)
- 在自己的linux下查看当前版本`cat /etc/redhat-release`
  ![1](E:\study information\myself\MySQL\1.png)
- 右击查看`网页源代码`，看全部信息，Centos为el，版本号需比linux版本低
- 创建路径`mkdir MySQL`，进入该目录`cd MySQL`
- `rd`引用安装包
- 安装：`rpm -ivh [安装包]`
  ![2](E:\study information\myself\MySQL\2.png)
- 

### 1.3开始安装

### 1.4开始登录

### 1.5设置配置文件

## 二.MySQL数据库基础

### 2.1什么是数据库
