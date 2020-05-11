# mybatis 整数0判断

mybatis中若参数status为int型0时，status==''为true.

如果确定status一定是int型，则直接判断null即可



```mysql
<where>  

    <if test="status != null ">  

        and status=#{status,jdbcType=NUMERIC}  

    </if>  

</where>
```

否则，进行如下判断

```mysql
<where>  

    <if test="status != null and status.toString() !='' ">  

        and status=#{status,jdbcType=NUMERIC}  

    </if>  

</where>
```

