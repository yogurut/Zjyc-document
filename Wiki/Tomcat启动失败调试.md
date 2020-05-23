# Tomcat windows环境启动失败问题调试-牛育林



## 问题描述

​        windows环境启动tomcat，是通过bin目录下面的startup.bat文件启动的，但是tomcat启动失败时会出现该窗口一闪而过的情况，此时是无法判断问题出现在哪里的。

## 解决方案

​      startup.bat启动tomcat时流程是：startup->catalina->setclasspath->catalina

 ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/20200523101805071.png)

​	   如果这3个bat文件里面有一个出现错误的话就是启动失败

​      先记事本打开startup.bat，找到最后一句话：:end，end代表结束，:end是一个标记，在后面加上一句pause (暂停等待的意思)；

再次执行startup.bat，就会看到如图，当按任意的键时cmd窗口又是一闪而过了。但是这可以确定了环境变量都是正确的。

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/20200523102048627.png)

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/20200523102117539.png)

​       为了更加详细的看到信息，可以再来更改一句：找到call "%EXECUTABLE%" start %CMD_LINE_ARGS% 把里面的start替换为run。

再来看看cmd窗口里面输出错误信息了

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/20200523110020197.png)