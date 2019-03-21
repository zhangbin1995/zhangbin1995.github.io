---
title: PageRequest过时替换
date: 2019-02-13 12:25:35
tags:
	- java
	- springboot
categories:
	- java
---

使用SpringbootJPA的PageRequest时提示已过时

```
PageRequest request = new PageRequest(page, size);
```

解决方法：

用PageRequest.of(page, size)替换。

```
PageRequest request = PageRequest.of(page, size);
```

顺便列出一个分页的查询方法代码：

```
	//订单列表
    @GetMapping("/list")
    public ResultVO<List<OrderDTO>> list(@RequestParam("openid") String openid,
                                         @RequestParam(value = "page", defaultValue = "0") Integer page,
                                         @RequestParam(value = "size", defaultValue = "10") Integer size){
        if(StringUtils.isEmpty(openid)){
            log.error("[查询订单列表] openid为空");
            throw new SellException(ResultEnum.PARAM_ERROR);
        }

        //PageRequest已经过时 用PageRequest.of替换
        //PageRequest request = new PageRequest(page, size);
        PageRequest request = PageRequest.of(page, size);
        Page<OrderDTO> orderDTOPage = orderService.findList(openid, request);

        return ResultVOUtil.success(orderDTOPage.getContent());
    }
```

orderDTOPage中会有三个最主要的方法：

* getContent: 获得该页的记录集合
* getTotalElements: 获得总记录条数
* getTotalPages： 获得总页数