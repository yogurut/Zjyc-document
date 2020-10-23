# docker安装及可视化工具shipyard安装

**1、前置条件**

  需要内核版本高于3.10的64位操作系统：（一般使用os7比较好）



  **安装步骤：**

 **1.**   **更新yum云：** 

   \# `yum update`

 **2.  yum安装docker：**

​   \# `sudo yum install docker`



**3. 启动Docker，并设置为开机启动：**

  \# `service docker start`

  \# `chkconfig docker on`

**4. 测试安装是否成功**

​  \# `docker version`



​    **截图（等下替换）：**

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1551406885723.png)





  

  **Docker图形界面Shipyard（汉化版）安装**

​    **1.设置防火墙：如果不设置防火墙的话，安装成功shipyard后，虽然能打开web页面，但是看不到本地容器和镜像**

*# `firewall*-*cmd* --*zone=public* --*permanent* --*add*-*port=2375/tcp`*

*提示：success*

*# firewall*-*cmd* –*reload*

*提示：success*

**2.安装镜像：**



\#   `docker pull rethinkdb`

​    \# `docker pull microbox/etcd`

​    \# `docker pull shipyard/docker-proxy`

​    \# `docker pull swarm`

​    \# `docker pull dockerclub/shipyard`

​    **3.运行脚本：**

\# `./docker-shipyard.sh`

[docker-shipyard.sh](https://file.devcloud.huaweicloud.com/detail/82861b78e5bf48d690bb6bee248c6462/2723341/list)

​    **4.测试是否成功**

​    访问地址如：http://服务器ip地址:8080 访问账号：admin,密码：shipyard

​    

​    **如果出现本地容器和镜像无法加载：**

​    **[deploy](https://file.devcloud.huaweicloud.com/detail/82861b78e5bf48d690bb6bee248c6462/2723345/list)
[shipyard_restart.sh](https://file.devcloud.huaweicloud.com/detail/82861b78e5bf48d690bb6bee248c6462/2723346/list)
**

​    **两个文件拷贝到服务器linux控制端依次输入**

​    \# `sh shipyard_restart.sh stop`

​    \# `sh shipyard_restart.sh rm`

​    \# `sh deploy`

​    在8080端口访问shipyard



​    如果下载镜像出现超时或者很慢：更换阿里镜像库

​    \# curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s [http://aad0405c.m.daocloud.io](http://aad0405c.m.daocloud.io/) 