---
title: 对null字段的两种处理
date: 2019-02-14 08:46:36
tags:
	- java
	- springboot
categories:
	- java
---

在查询订单列表时，有的订单详情为空返回值为null，不利于前端展示，如下：

```
{
    "code": 0,
    "msg": "成功",
    "data": [
        {
            "orderId": "1550025663184309586",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:41:06.000+0000",
            "updateTime": "2019-02-13T16:41:06.000+0000",
            "orderDetailList": null
        },
        {
            "orderId": "1550025830175838897",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:43:48.000+0000",
            "updateTime": "2019-02-13T16:43:48.000+0000",
            "orderDetailList": null
        }
    ]
}
```

可以看到上面**orderDetailList**的值为**null**。

这里我们可以给orderDTO添加注释**JsonInclude**来解决：

```
@JsonInclude(JsonInclude.Include.NON_NULL)
```

这样为空的属性就不会返回给前端了，运行结果：

```
{
    "code": 0,
    "msg": "成功",
    "data": [
        {
            "orderId": "1550025663184309586",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:41:06.000+0000",
            "updateTime": "2019-02-13T16:41:06.000+0000"
        },
        {
            "orderId": "1550025830175838897",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:43:48.000+0000",
            "updateTime": "2019-02-13T16:43:48.000+0000"
        }
    ]
}
```

也可以在**application.yml**中配置：

```
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    username: herobin
    password: Zhangbin1995@
    url: jdbc:mysql://101.132.138.185:3306/sell?characterEncoding=utf-8&useSSL=false
  jpa:
    show-sql: true
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    hibernate.ddl-auto: update
  # 配置所有的类只返回非空属性 
  jackson:
    default-property-inclusion: non_null
server:
  servlet:
    context-path: /sell

```

可是有的时候我们想要返回orderDetailList，还是值不为空，而是一个方括号。

可以在orderDTO中给orderDetailList赋初值。

```
List<OrderDetail> orderDetailList = new ArrayList<>();
```

运行结果：

```
{
    "code": 0,
    "msg": "成功",
    "data": [
        {
            "orderId": "1550025663184309586",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:41:06.000+0000",
            "updateTime": "2019-02-13T16:41:06.000+0000",
            "orderDetailList": []
        },
        {
            "orderId": "1550025830175838897",
            "buyerName": "admin",
            "buyerPhone": "123456",
            "buyerAddress": "11111",
            "buyerOpenid": "123456",
            "orderAmount": 3.2,
            "orderStatus": 0,
            "payStatus": 0,
            "createTime": "2019-02-13T16:43:48.000+0000",
            "updateTime": "2019-02-13T16:43:48.000+0000",
            "orderDetailList": []
        }
    ]
}
```

同理，String类型和int类型也都可以通过赋初值的方法避免null问题。

