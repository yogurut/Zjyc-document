# jenkins

## jenkins是什么？

Jenkins 是一个可扩展的持续集成引擎。

主要用于：

1. 持续，自动地构建/测试软件项目；
2. 监控一些定时执行的任务。

Jenkins拥有的特性包括：

1. 安装简单不需要数据库的支持；
2. 易于配置所有配置都给予web页面设置；
3. 分布式构建支持Jenkins能够让多台计算机一起构建/测试，并且可以通过Email通知；
4. 文件识别:Jenkins能够跟踪哪次构建生成哪些jar，哪次构建使用哪个版本的jar等；
5. 插件支持:支持扩展插件，你可以开发适合自己团队使用的工具。

## Jenkins的安装及简单配置：

- 在线安装：

​    \## http://pkg.jenkins-ci.org/redhat/

​    sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo

​    sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key

​    sudo yum -y install jenkins

- 修改Jenkins的端口号，通过vim /etc/sysconfig/Jenkins

​    ![1551338672671](https://gitee.com/yoguruto/image/raw/master/img/1551338672671.png)

​    service jenkins restart” 启动jenkins

- 然后浏览器中输入 服务器地址：端口号

​    默认密码位置

   ![1551338741650](https://gitee.com/yoguruto/image/raw/master/img/1551338741650.png)

- jenkins中配置JDK和maven

   需要在linux中安装配置路径为linux中的安装地址

![1551338826463](https://gitee.com/yoguruto/image/raw/master/img/1551338826463.png)

- 下载插件

  [Maven Integration plugin](https://wiki.jenkins.io/display/JENKINS/Maven+Project+Plugin) Maven管理

  [Publish Over SSH](http://wiki.jenkins-ci.org/display/JENKINS/Publish+Over+SSH+Plugin)     SSH远程服务器

  [Subversion Plug-in](https://wiki.jenkins.io/display/JENKINS/Subversion+Plugin)   SVN代码管理

- 配置SSH远程服务器：

​    ![1551338984907](https://gitee.com/yoguruto/image/raw/master/img/1551338984907.png)

​    ![1551339044912](https://gitee.com/yoguruto/image/raw/master/img/1551339044912.png)

-   创建项目

​      ![1551339096512](https://gitee.com/yoguruto/image/raw/master/img/1551339096512.png)

  ![1551339105092](https://gitee.com/yoguruto/image/raw/master/img/1551339105092.png)

​    ![1551339109757](https://gitee.com/yoguruto/image/raw/master/img/1551339109757.png)

​    ![1551339116894](https://gitee.com/yoguruto/image/raw/master/img/1551339116894.png)

  远程服务器部署：

​     ![1551339131648](https://gitee.com/yoguruto/image/raw/master/img/1551339131648.png)



  本服务器部署：

​    ![1551339142730](https://gitee.com/yoguruto/image/raw/master/img/1551339142730.png)

​    通过shell脚本执行

​    执行对应命令即可

​    最后Save保存

- 部署

​    点击按钮进行部署

​    ![1551339257909](https://gitee.com/yoguruto/image/raw/master/img/1551339257909.png)查看控制台输出日志

​    ![1551339272964](https://gitee.com/yoguruto/image/raw/master/img/1551339272964.png)

  后续待补充。