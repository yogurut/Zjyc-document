# Redis入门

# ** redis简介**

mysql数据库,数据以文件形式存储在硬盘里面。

  redis 远传数据服务的缩写,一款**内存高速缓存数据库**.该软件使用C语言编写,它的**数据模型为key-value**.它支持丰富的数据结构(类型),比如String ,list,hash,set,sorted set.**可持久化**,保证了数据安全.

  **缓存:**

  **有两种类型:数据缓存,页面缓存(smarty)**

  页面缓存:经常用在CMS(内存管理系统)里面,

  例如:新闻页面(**内容主题单一,集中**)适合做页面缓存

  数据缓存:经常用在页面的具体数据里面

  例如:商品页面的组成部分根据业务特点,**各个部分数据比较独立**,适合给他们分别做”数据缓存”.

  **使用缓存减轻数据库的负载.**

  **对应哪些短时间不变,且被频繁访问的数据,可以考虑使用缓存技术.**

# **redis安装**



   进入Redis安装包目录，注册服务：redis-server.exe --service-install redis.windows.conf --loglevel verbose


  ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667073300.png)


![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667143344.png)

   备注：通过以上面命令，会在window  Service列表出现”Redis”服务，但此服务不是启动状态，需要调下面命令启动服务。

   启动服务：redis-server.exe --service-start

   
  ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667180068.png)

   客户端调用: redis-cli.exe -h 127.0.0.1 -p 6379

  
  ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667213266.png)

   停止服务：redis-server.exe --service-stop

  
  ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667269733.png)

   卸载服务: redis-server.exe --service-uninstall

  


  ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/1555667313274.png)



  

# **redis数据类型操作：**

## 1.key的操作:

1.1key定义:

给存储在redis内存中的数据起得变量名字

 

1.2key的命名规范:

在redis里边,除了”\n” 和空格不能作为名字的组成内容外,其他内容都可以作为key的名字部分.名字长度不做要求.

例如: set **name** zhangsan;//name为key,zhangsan为value;

1.3key操作

| Key操作                   | 说明                                |
| ------------------------- | ----------------------------------- |
| **exists** key            | 测试指定key是否存在                 |
| **del** key1 ke2 …. keyN  | 删除指定key                         |
| **type** key              | 返回key的value类型                  |
| **keys** pattern          | 返回匹配指定模式的所有key           |
| **rename** oldkey newkey  | 重命名                              |
| **dbsize**                | 返回当前数据库的key数量             |
| **expire** key seconds    | 为key设置过期时间                   |
| **ttl** key               | 返回key的剩余过期秒数               |
| **select** db-index(0~15) | 选择数据库                          |
| **move** key db-index     | 将key从当前数据库移动到指定的数据库 |
| **flushdb**               | 删除当前数据库中所有的key           |
| **flushall**              | 删除所有数据库中所有的key           |

 

## 2.String类型操作

 

2.1 string是redis最基本的数据类型

 

redis的string可以包含任何数据.包括jpg图片或者序列化的对象

单个value值最大上限是1G字节

如果只用string类型,redis就可以被看作加上持久化特性的memcache

 

2.2string类型操作

| String类型操作                                  | 说明                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| **set** key value                               | 设置key对应的值为string类型的value                           |
| **mset** key1 value1 …. keyN valueN             | 一次设置多个key的值                                          |
| **mget** key1 key2 ….keyN                       | 一次获取多个key的值                                          |
| **incr** key(可以是新key,也可以是已经存在的key) | 对key的值做++操作,并返回新的值新key:创建该key并累加1,其值为1.  Incr num;  执行get  num 返回:1注:key的信息值类型要求必须为整型的 |
| **decr** key                                    | 对key的值做--操作,并返回新的值                               |
| **incrby** key integer                          | 加指定的值,并返回新的值                                      |
| **decrby** key integer                          | 减指定的值,并返回新的值                                      |
| **append** key value                            | 给指定key的字符串值追加value                                 |
| **substr** key start end                        | 返回截取过的key的字符串的值                                  |

 

## 3.List类型操作(有重复元素)

1.list类型其实就是一个双向链表.通过push,pop操作从链表的头部或者尾部添加/删除元素.这使得list既可以用作栈,也可以用作队列.

 

2.应用场合:

 

需求:获取最新的10个登录用户信息:

sql语句:

```mysql
select * from user order by logintime desc limit 10;
```



但是当数据很多的时候,全部数据都要收到影响,对数据库的负载较高.

如果使用list链表实现以上功能,可以在list链表中只保留最新的10个数据,每进来一个新的数据就删除一个旧数据.每次可以从链表中直接获取需要的数据.这样就可以极大节省各方面的资源消耗.

 

3.list类型的操作

 

| 操作                     | 说明                                                      |
| ------------------------ | --------------------------------------------------------- |
| **lpush** key string     | 在key对应list的头部添加字符串元素                         |
| **rpop** key             | 在list的尾部删除元素,并返回删除的元素                     |
| **llen** key 返回key     | 对应list的长度,key不存在返回0,若key的类型不是list返回错误 |
| **lrange** key start end | 返回指定区间内的元素,下标从0开始                          |
| **rpush** key string     | 在尾部添加                                                |
| **lpop** key             | 从头部删除,并返回删除的元素                               |
| **ltrim** key start end  | 截取list,保留指定区间内元素                               |

## 4.set集合类型(无重复元素)

1.redis 的set是string类型的无序集合

set元素最大可以包含(2的32次方-1)个元素

set集合除了基本的添加/删除操作,其他有用的操作还包括集合的取并集(union),交集,差集.

 

2.应用场合:

通过并,交,差可以很容易的实现类似qq中的好友推荐功能.

注意:集合中的元素不能重复.

 

3.set集合的操作

| 操作                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| **sadd** key member          | 添加一个string元素到key对应的set集合中,成功返回1;如果该元素已经存在,返回0,key对应的set不存在返回错误 |
| **srem** key member [member] | 从key对应set中移除给定元素,成功返回1                         |
| **smove** p1 p2 member       | 从p1对应set中移除member并添加到p2对应的set中                 |
| **scard** key                | 返回set的元素个数                                            |
| **sismember** key member     | 判断member是否在set中                                        |
| **sinter** key1 key2 … keyN  | 返回所有给定key的交集                                        |
| **sunion** key1 key2 .. keyN | 返回所有给定key的并集                                        |
| **sdiff** key1 key2 … keyN   | 返回所有给定key的差集                                        |
| **smembers** key             | 返回key对应set的所有元素,结果是无序的.                       |

## 5.Sort Set 排序集合(无重复元素)

1.和set集合一样 sort set 也是string类型元素的集合,

不同的是每个元素都会关联一个**权**

通过权值可以有序的获取集合中的元素.

 

2.应用场合:

获取热门帖子(回复量)信息:

```mysql
select * from message order by backnum desc limit 5;
```



可以利用sort set实现获取最热门的前5的帖子

**排序集合中的每个元素都是值****,****权的组合****.**

3.sort set排序类型操作

| 操作                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| **zadd** key score member       | 添加元素,元素在集合中存在则更新对应的score例如:  zadd countName 102 11 |
| **zrm** key member              | 删除指定元素,1表示成功,否则,返回0                            |
| **zincrby** key incr member     | 按incr幅度增加对应member的score值,并返回score的值            |
| **zrank** key member            | 返回指定元素在集合中的排名(下标),集合中元素按score从小到大排序的. |
| **zreverank** key member        | 返回指定元素在集合中的排名(下标),集合中元素按score从大到小排序的. |
| **zrange** key start end        | 类似lrange操作从集合中去指定区间的元素,返回的是有序的结果    |
| **zrevrange** key start end     | 同上,返回结果是按score的逆序排列的                           |
| **zcard** key                   | 返回集合中元素的个数                                         |
| **zscore** key element          | 返回给定元素对应的score                                      |
| **zremrangebyrank** key min max | 删除集合中排名在给定区间**(****包括****min****也包括****max)**的元素 |

 

## 6.Hash数据类型操作

1.hash数据类型存储的数据与mysql数据库中存储的一条记录极为相似.

 

2.hash类型操作

 

 

| 操作                                       | 说明                                        |
| ------------------------------------------ | ------------------------------------------- |
| **hset** key field value                   | 设置hash field为指定值,若key不存在,则先创建 |
| **hget** key field                         | 获取指定的hash field                        |
| **hmge**t key filed1….fieldN               | 获取全部的hash field                        |
| **hmset** key filed1 value1 …fieldN vlaueN | 同时设置hash的多个field                     |
| **hincrby** key field integer              | 将指定的hash field加上给定值                |
| **hexists** key field                      | 测试指定field是否存在                       |
| **hdel** key field                         | 删除指定的hash field                        |
| **hlen** key                               | 返回指定的hash的field的数量                 |
| **hkeys** key                              | 返回hash的所有field                         |
| **hvals** key                              | 返回hash的所有value                         |
| **hgetall** key                            | 返回hash的所有field和value                  |