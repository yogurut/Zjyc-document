# Tomcat配置安全优化

1.删除默认目录

安装完tomcat后，删除$CATALINA_HOME/webapps下默认的所有目录文件

```sh
rm -rf /usr/local/tomcat/webapps/*
```

2.用户管理

如果不需要通过web部署应用，建议注释或删除tomcat-users.xml下用户权限相关配置

```xml
<!--  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <user username="tomcat" password="<must-be-changed>" roles="tomcat"/>
  <user username="both" password="<must-be-changed>" roles="tomcat,role1"/>
  <user username="role1" password="<must-be-changed>" roles="role1"/>-->
```

3.隐藏tomcat版本信息

方法一：修改$CATALINA_HOME/conf/server.xml,在Connector节点添加server字段，示例如下

```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" 
               server="WS1.0"/>
```

访问测试：

```
# curl -I 127.0.0.1:8080HTTP/1.1 200 OK
Content-Type: text/html;charset=ISO-8859-1
Transfer-Encoding: chunked
Date: Thu, 29 Mar 2018 03:16:36 GMT
Server: WS1.0
```

浏览器测试：

```
 http://ip:8080/hello
测试结果显示并没隐藏Tomcat版本信息。
```

方法二：进入apache-tomcat目录lib下，找到catalina.jar，使用vim工具依次找到org/apache/catalina/util下的ServerInfo.properties

```xml
server.info=Apache Tomcat
server.number=server.built=
```

用户也可自定义修改server.info字段和server.number字段，示例修改如下所示

```
server.info=WS/1.0
server.number=1.0
server.built=Feb 7 2018 18:52:33 UTC
```

浏览器测试：

```xml
 http://ip:8080/hello
测试结果显示已隐藏Tomcat版本信息。5.自定义错误页面
修改$CATALINA_HOME/conf/web.xml,在/usr/local/tomcat/webapps/ROOT下自定义40x、50x等容错页面，防止信息泄露。
  <error-page>
       <error-code>404</error-code>
       <location>/404.html</location>
   </error-page>

   <error-page>
        <error-code>500</error-code>
        <location>/500.html</location>
   </error-page>
这里建议设置web.xml的error-page，指定返回页面。
```

4.关闭自动部署

如果不需要自动部署，建议关闭自动部署功能。

在$CATALINA_HOME/conf/server.xml中的host字段，修改unpackWARs=”false” autoDeploy=”false”。

```xml
      <Host name="localhost"  appBase="webapps"
            unpackWARs="false" autoDeploy="false">
       <!--   unpackWARs="true" autoDeploy="true">
       -->
```

6.禁止列目录（高版本默认已禁止）

修改$CATALINA_HOME/conf/web.xml

```xml
    <servlet>
        <servlet-name>default</servlet-name>
        <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
        <init-param>
            <param-name>debug</param-name>
            <param-value>0</param-value>
        </init-param>
        <init-param>
            <param-name>listings</param-name>
            <param-value>false</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```

7.AJP端口管理

AJP是为 Tomcat 与 HTTP 服务器之间通信而定制的协议，能提供较高的通信速度和效率。如果tomcat前端放的是apache的时候，会使用到AJP这个连接器。前端如果是由nginx做的反向代理的话可以不使用此连接器，因此需要注销掉该连接器。

```xml
    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <!-- <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
    -->
```

8.服务权限控制

tomcat以非root权限启动，应用部署目录权限和tomcat服务启动用户分离，比如tomcat以tomcat用户启动，而部署应用的目录设置为nobody用户750。

9.启用cookie的HttpOnly属性,使用HttpOnly提升Cookie安全性



修改$CATALINA_HOME/conf/context.xml，添加useHttpOnly="true",如下所示

```xml
<Context useHttpOnly="true">

    <!-- Default set of monitored resources -->
    <WatchedResource>WEB-INF/web.xml</WatchedResource>

    <!-- Uncomment this to disable session persistence across Tomcat restarts -->
    <!--    <Manager pathname="" />
    -->

    <!-- Uncomment this to enable Comet connection tacking (provides events
         on session expiration as well as webapp lifecycle) -->
    <!--    <Valve className="org.apache.catalina.valves.CometConnectionManagerValve" />
    -->
</Context>
```
启用安全cookie，防止xss跨站点攻击，tomcat6开始支持此属性，此处在context.xml中添加启用配置，context.xml配置即调用时生效不需要重启tomcat

配置cookie的secure属性，在web.xml中sesion-config节点配置cookie-config，此配置只允许cookie在加密方式下传输。

```xml
    <session-config>
        <session-timeout>30</session-timeout>
        <cookie-config>
                <secure>true</secure>
        </cookie-config>
    </session-config>
```

