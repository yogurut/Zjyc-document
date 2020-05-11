# ServletContext

1.1介绍

​    ServletContext官方叫servlet上下文。服务器会为每一个工程创建一个对象，这个对象就是ServletContext对象。这个对象全局唯一，而且工程内部的所有servlet都共享这个对象。所以叫全局应用程序共享对象。

   ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1565687168938.png)

1.2. 作用

1.   是一个域对象
2.   可以读取全局配置参数
3.   可以搜索当前工程目录下面的资源文件
4.   可以获取当前工程名字（了解）
1.2.1.  servletContext是一个域对象
1.2.1.1. 域对象介绍
域对象是服务器在内存上创建的存储空间，用于在不同动态资源（servlet）之间传递与共享数据。

1.2.1.2.      域对象方法

凡是域对象都有如下3个方法：

setAttribute(name,value);name是String类型，value是Object类型；往域对象里面添加数据，添加时以key-value形式添加

getAttribute(name);根据指定的key读取域对象里面的数据

removeAttribute(name);根据指定的key从域对象里面删除数据
应用：解决浏览器重复登录

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1565687372224.png)

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1565687512721.png)通过比对两个时间来判断当前登录账户是否存在重复登录情况