---
title: Springboot中Jpa的使用
date: 2019-02-01 18:57:47
tags:
	- java
	- springboot
categories:
	- java
---

springboot中可以通过使用Jpa实现dao层的简单配置，使用jpa后可以省略复杂的sql语句编写，自带简单的增删改查功能。

## application.yml的配置

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
```

## dao类的编写

dao接口直接集成JpaRepository接口即可。

```
package com.imooc.dao;

import com.imooc.entity.ProductCategory;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

/**
 * Created by binzhang on 19/1/21.
 */
public interface ProductCategoryDao extends JpaRepository<ProductCategory, Integer> {

    List<ProductCategory> findByCategoryTypeIn(List<Integer> categoryTypeList);

}
```

## dao层测试方法

```
package com.imooc.dao;

import com.imooc.entity.ProductCategory;
import org.junit.Assert;
import org.junit.Ignore;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.Arrays;
import java.util.List;
import java.util.Optional;

import static org.junit.Assert.*;

/**
 * Created by binzhang on 19/1/21.
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class ProductCategoryDaoTest {

    @Autowired
    private ProductCategoryDao pcDao;

    @Test
    @Ignore
    public void findOneTest(){

        ProductCategory productCategory = pcDao.findById(1).get();
        System.out.println(productCategory.toString());

    }

    @Test
    public void findAllTest(){
        List<ProductCategory> pcList = pcDao.findAll();
        for (ProductCategory pc : pcList){
            System.out.println(pc.getCategoryName());
        }
    }
    
    @Test
    public void saveTest(){
        ProductInfo productInfo = new ProductInfo();
        productInfo.setProductId("123456");
        productInfo.setProductName("皮蛋粥");
        productInfo.setProductPrice(new BigDecimal(3.2));
        productInfo.setProductStock(100);
        productInfo.setProductDescription("很好喝的粥");
        productInfo.setProductIcon("xxx.png");
        productInfo.setProductStatus(0);
        productInfo.setCategoryType(2);

        ProductInfo result = productInfoDao.save(productInfo);
        Assert.assertNotNull(result);
    }

    @Test
    public void findByCategoryTypeInTest(){
        List<Integer> list = Arrays.asList(2,3,4);
        List<ProductCategory> result = pcDao.findByCategoryTypeIn(list);
        Assert.assertNotEquals(0,result.size());
    }

}
```

这里测试了 单个查找的`findById()`方法和`findAll()`方法，注意这里的`findById()`方法返回的是`Optional<T>`,可以进到`JpaRepository`的`CrudRepository`里看到.

```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.data.repository;

import java.util.Optional;

@NoRepositoryBean
public interface CrudRepository<T, ID> extends Repository<T, ID> {
    <S extends T> S save(S var1);

    <S extends T> Iterable<S> saveAll(Iterable<S> var1);

    Optional<T> findById(ID var1);

    boolean existsById(ID var1);

    Iterable<T> findAll();

    Iterable<T> findAllById(Iterable<ID> var1);

    long count();

    void deleteById(ID var1);

    void delete(T var1);

    void deleteAll(Iterable<? extends T> var1);

    void deleteAll();
}
```

所以在findById()后我们还要用get()方法才能得到我们想要查询的对象。

查看get方法的源码：

```
public T get() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
}
```

可以看到当查询结果为空时会抛出异常，所以在我们的代码中不要忘记做判断抛出异常。

## 分页方法的使用

使用分页方法要将返回类型定义为PageAble。

dao方法：

```
/**
 * Created by binzhang on 19/2/1.
 */
public interface OrderMasterDao extends JpaRepository<OrderMaster,String>{

    Page<OrderMaster> findByBuyerOpenid(String buyerOpenId, Pageable pageable);

}
```

测试方法：

```
@Test
public void findByBuyerOpenId() throws Exception {
    /** PageRequest实现了PageAble接口 */
    PageRequest pageRequest = new PageRequest(0,1);
    Page<OrderMaster> request = orderMasterDao.findByBuyerOpenid("123321",pageRequest);
    //这个是总条数 不是分页返回的条数
    System.out.println(request.getTotalElements());
    System.out.println(request.getContent().get(0).getBuyerName());
}
```


