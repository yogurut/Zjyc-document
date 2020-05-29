# CentOS7.0以上版本系统自带防火墙使用
## 一、查看防火墙状态

查看防火墙状态 `systemctl status firewalld`

开启防火墙 `systemctl start firewalld`

关闭防火墙 `systemctl stop firewalld`

若遇到无法开启

先用：`systemctl unmask firewalld.service`

然后：`systemctl start firewalld.service`

## 二、查看对外开放的端口状态

查询已使用的端口：`netstat –anp`

查询指定端口开放状态：`firewall-cmd --query-port=80/tcp`

yes，表示开启；no表示未开启。

## 三、对外开放端口

开放指定的端口：

`firewall-cmd --add-port=80/tcp --permanent`

重载开放的端口：

`firewall-cmd --reload`

查询指定端口是否开启成功：

`firewall-cmd --query-port=80/tcp`

## 四、对外关闭端口

关闭指定的端口

`firewall-cmd --permanent --remove-port=80/tcp`

重载关闭的端口：

`firewall-cmd --reload`

查询指定端口是否关闭成功：

`firewall-cmd --query-port=80/tcp`