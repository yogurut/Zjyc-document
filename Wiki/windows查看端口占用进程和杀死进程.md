# windows查看端口占用进程和杀死进程

杀死进程占用的端口号方法

查询端口号：

netstat -ano | findstr 需要查询的端口号  

杀死占用端口号的进程

taskkill /pid 占用的进程号 /f

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1588063305276.png)