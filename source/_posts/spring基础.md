---
title: spring基础
date: 2018-01-29 15:02:43
tags:
	- spring
	- java
categories:
	- java
---

**Spring：**轻量级的控制反转（IOC）和面向切面（AOP）的容器框架.
> * -从大小与开销两方面而言Spring都是轻量的
> * -通过控制反转（IOC）的技术达到松耦合的目的
> * -提供了面向切面的编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发
> * -包含并管理应用对象的配置和生命周期，这个意义上是一种容器
> * -将简单的组件配置、组合成为复杂的应用，这个意义上是框架

**IOC：**控制反转，控制权的转移，应用程序本身不负责依赖对象的创建和维护，而是由外部容器负责创建和维护

**DI（依赖注入）**是其一种实现方式

**目的：**创建对象并且组装对象之间的关系

控制被反转后，获得依赖对象的过程由自身管理变成了由IOC容器主动注入。

**注入方式：**
> * 设值注入 （set） 
> * 构造注入

**bean的作用域：**
> * singleton：单例，指一个Bean容器中只存在一份
> * prototype：每次请求（每次使用）创建新的实例，destory方式不生效
> * request：每次http请求创建一个实例且仅在当前request内有效
> * session：同上，每次http请求创建，当前session内有效
> * global session：基于portlet的web中有效，如果是在web中，同session     

**bean的自动装配（Autowiring）**
> * No：不做任何操作
> * byname：根据属性名自动装配。此选项将检查容器并根据名字查找与属性完全一致的bean，并将其与属性自动装配
> * byType：如果容器中存在一个与指定属性类型相同的bean，那么将于该属性自动装配；如果存在过个该类型bean，那么抛出异常，并指出不能使用byType方式进行自动装配；如果没有找到相匹配的bean，则什么事都不发生
> * Constructor：与byType方式类似，不同之处在于它应用于构造器参数。如果容器中没有找到与构造器参数类型一致的bean，那么抛出异常

**注解：**

@configuration是一个通用注解，可用于任何bean

@Repository，@Service，@Controller是更有针对性的注解
> * @Repository通常用于注解Dao类，即持久层
> * @Service通常用于注解Service类，即服务层
> * @Controller通常用于Controller类，即控制层（MVC）

通常情况下自动查找的Spring组件，其scope是singleton，可以设置：

```
@scope("prototype")
@Repository
public class stuDao imp.........
```

**@Required注解适用于bean属性的setter方法（不常用）**

这个注解仅仅表示，受影响的bean属性必须在配置时被填充，通过在bean定义或通过自动装配一个明确的属性值

**@Autowired：**“传统”的setter方法。这种自动注入，既可以用在setter方法上，也可以用在成员变量和构造器上。用在成员变量的话那么就不需要定义setter方法了。

默认情况下，如果因找不到合适的bean将会导致autowired失败抛出异常，可以通过下面的方式避免

```
private class UserService{
    private UserDao userDao;
    @Autowired(required=false)
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
}
```

在使用Spring框架中@Autowired标签时默认情况下使用 @Autowired 注释进行自动注入时，Spring 容器中匹配的候选 Bean 数目必须有且仅有一个。当找不到一个匹配的 Bean 时，Spring 容器将抛出BeanCreationException 异常，并指出必须至少拥有一个匹配的 Bean。Spring 允许我们通过 @Qualifier 注释指定注入 Bean 的名称，这样歧义就消除了，可以通过下面的方法解决异常。

@Qualifier("XXX") 中的 XX是 Bean 的名称，所以 @Autowired 和 @Qualifier 结合使用时，自动注入的策略就从 byType 转变成 byName 了。@Autowired 可以对成员变量、方法以及构造函数进行注释，而 @Qualifier 的标注对象是成员变量、方法入参、构造函数入参

**基于java的容器注解**
`@Bean`标识一个用于配置和初始化一个由`SpringIoC`容器管理的新对象的方法，类似于XML配置文件的`<bean/>`

可以在`Spring`的`@Component`注解的类中使用`@Bean`注解任何方法（仅仅是可以）

上一点中，通常使用的是`@Configuration`.

`@Bean`中可以有可选项`name，initMethod，destoryMethod`等

```
@Configuration
public class StoreConfig{
    @Bean(name = "store",initMethod = "",destoryMethod = "")
    public Store stringStore(){
        return new StringStore();
    }
}
```

在`applicationContext.xml`中配置数据库文件：

```
<!-- 引入外部属性文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!-- 配置C3P0连接池: -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

@Resource(name = "")
public void setStudent(Student student){
    this.student = student;
}

```

如果没有显式的指定`@Resource`的`name`，默认的名称是从属性名或者`setter`方法得出.

**AOP：**Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术

主要的功能是：
> * 日志记录
> * 性能统计
> * 安全控制
> * 事务处理
> * 异常处理等等

AOP实现方式
> * 预编译：AspectJ
> * 运行期动态代理（JDK动态代理、CGLib动态代理）：SpringAOP、JbossAOP

**AOP几个相关概念：**
> * 切面（Aspect）：一个关注点的模块化，这个关注点可能会横切多个对象
> * 连接点（Joinpoint）：程序执行过程中的某个特定的点
> * 通知（Advice）：在切面的某个特定的连接点上执行的动作
> * 切入点（Pointcut）：匹配连接点的断言，在AOP中通知和一个切入点表达式关联
> * 引入（Introduction）：在不修改类代码的前提下，为类添加新的方法和属性
> * 目标对象（Target Object）：被一个或者多个切面所通知的对象
> * AOP代理（AOP Proxy）：AOP框架创建的对象，用来实现切面契约（aspect contract）（包括通知方法执行等功能）
> * 织入（Weaving）：把切面连接到其他的应用程序类型或者对象上，并创建一个被通知的对象，分为：编译时织入、类加载时织入、执行时织入

**Advice的类型：**
> * 前置通知（Before Advice）：在某连接点（join point）之前执行的通知，但不能阻止连接点前的执行（除非他抛出一个异常）
> * 返回后通知（After returning advice）：在某连接点（join point）正常完成后执行的通知
> * 抛出异常后通知（After throwing advice）：在方法抛出异常退出时执行的通知
> * 后通知（After（finally）advice）：当某连接点退出的时候执行的通知（不论是正常返回还是异常返回）
> * 环绕通知（Around Advice）：包围一个连接点（join point）的通知

```
<aop:config>
    <aop:aspect id="testAspectAOP" ref="testAspect">
            <aop:pointcut id="testService" expression="execution(* com.service..(..))" id="p1"/>
            <aop:before method="before" point-ref="p1"/>
    </aop:aspect>
</aop:config>
```

**Spring的AOP实现**
纯java实现，无需特殊的编译过程，不需要控制类加载器层次

目前只支持方法执行连接点（通知Spring Bean的方法执行）

不是为了提供最完整的AOP实现；而是侧重于提供一种AOP实现和Spring IoC容器之间的整合，用于帮助解决企业应用中的常见问题

**Schema——based AOP**
Spring所有的切面和通知器都必须放在一个`<aop:config>`内（可以配置包含多个`<aop:config>`元素），每一个`<aop:config>`可以包含`pointcut`，`advisor`和`aspect`元素（他们必须按照这个顺序进行声明）

`<aop:config>`风格的配置大量使用了`Spring`的自动代理机制

**配置切面aspect**

```
<bean id="testAspect" class="com.imooc.aop.schema.advice.TestAspect"></bean>

<aop:config>
    <aop:aspect id="testAspectAOP" ref="testAspect">
    
    </aop:aspect>
</aop:config>
```

**切入点（pointcut）**

```
execution(public * *(..))：切入点为执行所有public方法时
execution(* set*(..))：切入点为执行所有set开始的方法时
execution(* com.service.AccountService.*(..))：切入点为执行AccountService类中的所有方法时
execution(* com.service..(..))：切入点为执行com.service包下的所有方法时
execution(* com.service...(..))：切入点为执行com.service包及其子包下的所有方法时
<aop:config>
    <aop:aspect id="testAspectAOP" ref="testAspect">
            <aop:pointcut id="testService" expression="execution(* com.service..(..))" id="p1"/>
    </aop:aspect>
</aop:config>
```

**Introductions**
简介允许一个切面声明一个实现指定接口的通知对象，并且提供了一个接口实现类来代替这些对象

由`<aop:aspect>`中的`<aop:declare-parents>`元素声明该元素用于声明所匹配的类型拥有一个新的parent

advisor就像一个小的自包含的方面，只有一个advice

切面自身通过一个bean表示，而且必须实现某个advice接口，同时，advisor也可以很好的利用AspectJ的切入点表达式





