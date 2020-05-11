# MyBatis JdbcType

## MyBatis包含的JdbcType类型，主要有下面这些，大致了解一下即可：

BIT、FLOAT、CHAR  、TIMESTAMP 、 OTHER 、UNDEFINEDTINYINT 、REAL 、VARCHAR 、BINARY 、BLOB  NVARCHAR、SMALLINT 、DOUBLE 、LONGVARCHAR 、VARBINARY 、CLOB、NCHAR、INTEGER、  NUMERIC、DATE 、LONGVARBINARY 、BOOLEAN 、NCLOB、BIGINT 、DECIMAL 、TIME  、NULL、CURSOR

## 上述JdbcType类型和Java类型的对应关系，可以参照下面的列表，不过不同数据库的JdbcType多少有些出入.

JDBC Type             Java  Type

CHAR                 String

VARCHAR              String

LONGVARCHAR         String

NUMERIC              java.math.BigDecimal

DECIMAL              java.math.BigDecimal

BIT                   boolean

BOOLEAN              boolean

TINYINT               byte

SMALLINT            short

INTEGER              INTEGER

BIGINT               long

REAL                 float

FLOAT                double

DOUBLE              double

BINARY               byte[]

VARBINARY            byte[]

DATE                java.sql.Date

TIME                java.sql.Time

TIMESTAMP            java.sql.Time

CLOB                Clob

BLOB                Blob

ARRAY               Array

DISTINCT            mapping of underlying type

STRRUCT            Struct

REF                 Ref

DATALINK          java.net.URL

## JdbcType类型的作用

在Mybatis明文建议在映射字段数据时需要将JdbcType属性加上，这样相对来说是比较安全的。

```mysql
<insert id="saveRole">
           insert into role values (
                    #{roleId},
                    #{name},
                    #{remarks},
                    #{orderNo},
                    #{createBy,jdbcType=VARCHAR},
                    #{createDept,jdbcType=VARCHAR},
                    #{createTime,jdbcType=DATE},
                    #{updateBy,jdbcType=VARCHAR},
                    #{updateTime,jdbcType=DATE}
         )
</insert>
```

​    这样，保证了前四种是不能为空的前提下，而后面几项为空时也不至于程序报错。如果createBy为空，插入的时候mybatis不知道具体转换成什么jdbcType类型，通常会使用一个默认设置，虽然默认配置一般情况下不会出错，但是遇到个别情况还是会有问题的。Mybatis经常出现的：无效的列类型:  1111 错误，就是因为没有设置JdbcType造成的。

