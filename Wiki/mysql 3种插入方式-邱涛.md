表要求：有PrimaryKey，或者unique索引

insert into：	      存在（报错）	  不存在（插入）
 
insert ignore：	    存在（忽略）	  不存在（插入） 	   

replace	into：       存在（替换）	  不存在（插入）


测试用例：

以t_cont表为例  ID字段是PrimaryKey，APPLY_CODE字段是unique

insert into测试：

insert into t_cont (CREATER, APPLY_CODE) VALUES ("张三", 123);

> Affected rows: 1

> 时间: 0.03s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

再次执行：

insert into t_cont (CREATER, APPLY_CODE) VALUES ("张三", 123);

> 1062 - Duplicate entry '123' for key 'NewIndex1'

> 时间: 0.028s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

insert ignore测试：

insert ignore t_cont (CREATER, APPLY_CODE) VALUES ("李四", 123);

> Affected rows: 0

> 时间: 0.033s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

再次执行：

insert ignore t_cont (CREATER, APPLY_CODE) VALUES ("李四", 123456);

> Affected rows: 1

> 时间: 0.031s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

> 822， 李四， 123456  （id从820变为822，因为中间忽略了一次）

replace into测试：

replace into t_cont (CREATER, APPLY_CODE) VALUES ("王五", 54321);

> Affected rows: 1

> 时间: 0.086s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

> 822， 李四， 123456

> 823， 王五， 54321

再次执行：

replace into t_cont (CREATER, APPLY_CODE) VALUES ("赵六", 54321);

> Affected rows: 2  （删除一个，新增一个，所以是2。  如果大于1，一定有替换，等于1，只是新增一条）

> 时间: 0.037s

select ID, CREATER, APPLY_CODE from t_cont;

> 820， 张三， 123

> 822， 李四， 123456

> 824， 赵六， 54321
