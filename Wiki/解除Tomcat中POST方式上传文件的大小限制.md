# 解除Tomcat中POST方式上传文件的大小限制-程远博

1、tomcat目录下的conf文件夹下，server.xml 文件中以下的位置中添加maxPostSize参数

![img](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/202001/1578474874332.png)

当maxPostSize=-1时，POST方式上传的文件大小不会被限制。



2、查看 servletContext.xml 中文件上传拦截大小是否过小（注意：value的值单位是字节，超过此值会被拦截）

![img](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/202001/1578474988252.png)

