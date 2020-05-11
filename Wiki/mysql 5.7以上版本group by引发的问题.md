# mysql 5.7以上版本group by引发的问题-刘诗哲

mysql 5.7版本默认开启ONLY_FULL_GROUP_BY，使用groupBy会报

SELECT list is not in GROUP BY clause and contains nonaggregated column这种错误。

**解决方法：**

1.执行 SELECT @@GLOBAL.sql_mode; 查看是否有ONLY_FULL_GROUP_BY，如果有，修改my.cnf文件

2.linux一般存放在 /etc/my.cnf 或/etc/mysql/my.cnf下

3.vi命令打开my.cnf文件 入  vi /etc/my.cnf

4.按insert键使用插入模式，光标移动到最下面 添加

SET sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE'

5.重启mysql服务

