# windows mysql 5.7以上本group by引发的问题-申俊洋

mysql 5.7解压缩版本默认开启ONLY_FULL_GROUP_BY，使用groupBy会报这种错误。

![img](https://devcloud.huaweicloud.com/wiki/v1/wiki/uploads/82861b78e5bf48d690bb6bee248c6462/201912/1577762980484.png)

解决办法：

在MySQL的解压安装目录下面的my.ini文件增加

```mysql
[mysql]

default-character-set=utf8

set sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```





```mysql
[mysqld]

character-set-server=utf8

default-storage-engine=INNODB

sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```





然后重启MySQL服务

数据库管理工具执行SELECT @@GLOBAL.sql_mode;

得到:STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

则表示问题已解决