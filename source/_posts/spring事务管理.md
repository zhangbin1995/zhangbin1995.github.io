---
title: spring事务管理
date: 2018-02-02 12:45:29
tags:
	- spring
	- java
categories:
	- java
---

### 什么是事务：
事务指的是逻辑上的一组操作，这组操作要么全部成功，要么全部失败.（银行转账）

#### 事务的特性：
**原子性 一致性 隔离性 持久性**

> * 原子性：原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生

> * 一致性：一致性指事务前后数据的完整性必须保持一致

> * 隔离性：隔离性指多个用户并发访问数据库时，一个用户的事务不能被其他用户的事务所干扰，多个并发事务之间数据要相互隔离

> * 持久性：持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，即使数据库发生故障也不应该对其有任何影响

**Spring事务管理高层抽象主要包括3个接口**
> * PlatformTransactionManager：事务管理器
> * TransactionDefinition：事务定义信息（隔离、传播、超时、只读）
> * TransactionStatus：事务具体运行状态

Spring为不同的持久化框架提供了不同PlatformTransactionManager接口实现

DataSourceTransactionManager：使用Spring JDBC或iBatis进行持久化数据时使用

HibernateTransactionManager：使用Hibernate3.0版本进行持久化数据时使用

### Spring支持两种方式事务管理
> * 编程式的事务管理:在实际应用中很少使用,通过TransactionTemplate手动管理事务
> * 通过XML配置声明式事务:开发中推荐使用（代码侵入性最小）
    Spring的声明式事务是通过AOP实现的

#### 编程式的事务管理：

配置文件编写：

```
<!-- 配置事务管理器 -->
<bean id = "transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionmanager">
    <property name = "dataSource" ref = "dataSource"/>
</bean>
<!-- 配置事务管理的模板：Spring为了简化事务管理的代码而提供的类 -->
<bean id = "transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
    <property name = "transactionManager" ref = "transactionManager"/>
</bean>
```

代码文件编写：

```
public void transfer(final String out, final String in, final Double money){
    transactionTemplate.execute(new TransactionCallbackWithoutResult){
        @override
        protected void doInTransactionWithoutResult(TransactionStatus transactionStatus){
            accountDao.outMoney(out, money);
            int i = i/0;//用于异常时检测事务完整性
            accountDao.inMoney(in, money);
        }
    }
}
```

#### 声明式的事务管理：

声明式事务管理方式一：TransactionProxyFactoryBean   （不常用）

```
<!-- 配置事务管理器： -->
<bean id = "transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionmanager">
    <property name = "dataSource" ref = "dataSource"/>
</bean>
<!-- 配置业务层的代理： -->
<bean id = "acountServiceProxy" class = "org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
    <!-- 配置目标对象 -->
    <property name = "target" ref = "accountService"/>
    <!-- 注入事务管理器 -->
    <property name = "transactionManager" ref = "transactionManager"/>    
    <!-- 注入事务属性 -->
    <property name = "transactionAttributes">
        <props>
            <!-- prop的格式：
                        * PROPAGATION  ： 事务的传播行为
                        * ISOLATION  ： 事务的隔离级别
                        * readOnly  ： 只读（不可以进行修改，插入，删除）
                        * -Exception  ： 发生哪些异常回滚事务
                        * +Exception： 发生哪些异常不回滚
             -->
            <prop key="transfer">PROPAGATION_REQUIRED,redaOnly,...</prop>
        </props>
    </property>
</bean>
```

声明式事务管理方式二：基于AspectJ使用xml方式  基于tx/aop

```
<bean id = "transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionmanager">
    <property name = "dataSource" ref = "dataSource"/>
</bean>
<tx:advice id = "txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="transfer" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
<aop:config>
    <aop:pointcut expression="execution(* cn.service.*Service.*(..))" id="serviceMethod"/>
    <aop:advisor pointcut-ref="serviceMethod" advice-ref="txAdvice"/>
</aop:config>
```

声明式事务管理方式三：基于注解的事务管理

配置文件编写：

```
<!-- 配置事务管理器 -->
<bean id = "transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionmanager">
    <property name = "dataSource" ref = "dataSource"/>
</bean>
<!-- 开启注解事务 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
使用的时候在需要事务的地方（通常是service层）加注解@Transactional
```
