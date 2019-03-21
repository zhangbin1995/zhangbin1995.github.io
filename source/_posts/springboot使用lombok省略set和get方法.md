---
title: springboot使用lombok省略set和get方法
date: 2018-07-22 14:54:47
tags:
	- java
	- springboot
categories:
	- java
---

# 使用lombok省略setget方法

首先在pom中加入lombok依赖包

pom.xml

```
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
</dependency>
```

第二步要在preferences里的plugins中安装lombok插件

![](/uploads/190122java1/1.png)

这样就可以用过lombok的`@Data`注解省略set和get方法了

ProductCategory.java

```
/**
 * 类目
 * Created by binzhang on 18/7/21.
 */
@Entity
//用这个注解才能实现动态更新（update_time的更新）
@DynamicUpdate
@Data
public class ProductCategory {
    @Id
    @GeneratedValue
    private Integer categoryId;

    // 类目名字
    private String categoryName;

    // 类目编号
    private Integer categoryType;

    private Date createTime;

    private Date updateTime;

    @Override
    public String toString() {
        return "ProductCategory{" +
                "categoryId=" + categoryId +
                ", categoryName='" + categoryName + '\'' +
                ", categoryType=" + categoryType +
                ", createTime=" + createTime +
                ", updateTime=" + updateTime +
                '}';
    }
}
```