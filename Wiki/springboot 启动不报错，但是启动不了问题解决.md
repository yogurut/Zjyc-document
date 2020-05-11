# springboot 启动不报错，但是启动不了问题解决

问题：springboot启动报错时无错误信息，如下图![](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201908/1566816197870.png)

解决办法：在SpringApplication.java的run()方法捕获异常时添加断点，启动项目根据异常信息来解决问题。

![](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201908/1566816447320.png)

![](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201908/1566816497571.png)





已知异常及解决：

​        1.logback-spring.xml解析错误![](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201908/1566816633803.png)

将logback-spring.xml中文件编码由utf-8改为gbk![](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201908/1566816732376.png)

