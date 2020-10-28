# Mysql 优化方案
like “%aaa%” 不会使用索引，like “aaa%”可以使用索引

NOT IN和<>操作都不会使用索引将进行全表扫描。NOT IN可以NOT EXISTS代替，id<>3则可使用id>3 or id<3来代替。

对查询进行优化，要尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

应尽量避免在 where 子句中使用 or 来连接条件，如果一个字段有索引，一个字段没有索引，将导致引擎放弃使用索引而进行全表扫描，
如：select id from t where num=10 or Name = 'admin'
可以这样查询：
select id from t where num = 10
union all
select id from t where Name = 'admin'

能用 between 就不用 in

能用 exists 就不用 in

`select id from t where num/2 = 100  应改为: select id from t where num = 100*2`

不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引


