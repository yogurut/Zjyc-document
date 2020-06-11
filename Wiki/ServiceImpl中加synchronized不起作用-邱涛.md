事件起因： 

在一个ServiceImpl类中某save方法上增加synchronized保证线程安全

public synchronized Map<String, Object> save(xxx){};

结果发现synchronized未起作用。



排查并得出结果：

由于spring的aop，会在save方法之前开启事务，如下（@Transactional注解一样）：

<tx:advice id="txAdvice" transaction-manager="transactionManager">

        <tx:attributes>
        
            <tx:method name="add*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="create*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="save*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="revoke*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="cancel*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="insert*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="modify*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="update*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="edit*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="clear*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="reset*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="delete*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="submit*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="stop*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="del*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="call*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="upload*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="pass*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="reject*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="back*" propagation="REQUIRED" rollback-for="Exception"/>
            
            <tx:method name="*" read-only="true"/>
            
        </tx:attributes>
        
    </tx:advice>


之后再加锁，当锁住的代码执行完成后，在提交事务。

因此synchronized代码块执行是在事务之内执行的，可以推断在代码块执行完时，事务还未提交，其他线程进入synchronized代码块后，读取的库存数据不是最新的。
