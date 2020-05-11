# Tomcat Session共享方案



1. **使用Tomcat内置的Session复制方案**

​      tomcat conf目录下  server.xml中将下图中注释放开

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1560334502550.png)

​      项目web.xml文件中添加节点<distributable/>

  2.**使用Redis实现session共享**

  **tomcat** lib目录下 添加三个jar包



![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1560335162902.png)

​    tomcat conf目录下，context.xml中添加节点



```xml
<Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />
<Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"
         host="localhost" <!-- optional: defaults to "localhost" -->
         port="6379" <!-- optional: defaults to "6379" -->
         database="0" <!-- optional: defaults to "0" -->
         maxInactiveInterval="60" <!-- optional: defaults to "60" (in seconds) -->
         sessionPersistPolicies="PERSIST_POLICY_1,PERSIST_POLICY_2,.." <!-- optional -->
         sentinelMaster="SentinelMasterName" <!-- optional -->
         sentinels="sentinel-host-1:port,sentinel-host-2:port,.." <!-- optional --> />
```

