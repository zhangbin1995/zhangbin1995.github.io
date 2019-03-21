---
title: SpringBoot初体验
date: 2018-12-27 19:09:40
tags: 
	- spring
	- java
	- springboot
categories:
	- java
---

首先SpringBoot的开发有两种主流的软件Idea和Spring Tool Suite（感觉就是集成了spring开发环境的eclipse）。
这里我们选用后者。可在springboot的官网spring.io里下载。
配置好jdk和maven的配置信息。

![](/uploads/181227springboot/1.png)

创建项目 `new -> spring starter project`
输入配置信息，这里我们选用了springboot2.1.1版本
选中我们需要的部分包。

![](/uploads/181227springboot/2.png)

另外还要注意最好将我们的maven配置改为阿里云镜像仓库(maven安装目录下的conf/setting.xml中)。

![](/uploads/181227springboot/3.png)

application.properties是springboot非常核心的一个部分。
几乎所有的配置信息都会写在这里面包括端口，数据库，mybatis，redis，验证码等等。不过在初始编辑application.properties时写中文注释可以会乱码，编码改为utf8即可。

修改pom文件，写入我们需要的jar包依赖，和ssm比较，因为我们新建的时候预先引入了web和mybatis两个模块，所以这两个不用再次引入，因为springboot自带测试所以junit也不需要了(去掉spring，mybatis，junit)。
将dao entity mapper创建好后在测试用例中测试报错如下

```
java.lang.AbstractMethodError: Method com/mchange/v2/c3p0/impl/NewProxyPreparedStatement.isClosed()Z is abstract
```

将pom中的c3p0镜像替换后问题得到解决

![](/uploads/181227springboot/4.png)

将全部package及webapp写好后启动springboot发现跳转到了测试接口hello springboot中，发现是将value写成了name（小坑注意）。

![](/uploads/181227springboot/5.png)

再次访问index接口发现前端报错404。

![](/uploads/181227springboot/6.png)

这里是因为以前的springboot的版本是1.5.8，这里我们使用了2.1.1.
在新版本中WebMvcConfigurationAdapter已经废弃，使用了系统建议的WebMvcConfigurationSupport也出现了上图的问题。考虑应该是静态资源的配置问题和视图跳转控制器的问题，也就是原属于WebMvcConfigurationAdapter的addResourceHandlers方法和addViewController方法没有起到应该起到的作用。
解决方法：
1.解决视图跳转问题，不再采用继承WebMvcConfigurationSupport的方式，改为直接实现WebMvcConfigurer接口，将生成视图解析器由方法转为@Bean注解的类生成

![](/uploads/181227springboot/7.png)

跳转成功解决。
2.跳转成功解决后发现前端页面加载静态资源出错

![](/uploads/181227springboot/8.png)

这里其实是我们的addResourceHandlers方法编写错误了，其实重写的时候并不需要对resource进行重写，空方法即可。但是这里我们要写好对图片路径的拼接。（这里是替代ssm中在tomcat的server.xml中docBase配置的替代）

```
	/**
	 * 静态资源配置
	 */
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		//registry.addResourceHandler("/resources/**").addResourceLocations("/resources/");
		registry.addResourceHandler("/upload/**").addResourceLocations("file:/Users/binzhang/Documents/shopImages/upload/");
	}
```

此时发现前端还是访问不到我们的图片，在network中可以看到url
http://localhost:8080/upload/item/shop/1.jpg
直接拷贝到浏览器也发现找不到该图片
这是因为在application.properties中我们配置了前缀

```
#加入/o2o前缀
server.servlet.context-path=/o2o
```

所以我们也要将项目的前缀追加到我们访问图片的路径地址中
http://localhost:8080/o2o/upload/item/shop/1.jpg
这样我们就可以访问到了。
在代码中添加前缀的步骤(前端实现)：
1.在common.js中加入getContextPath方法

```
/**
 * 获取项目的ContextPath以便修正图片路由让其正常显示
 * @returns
 */
function getContextPath(){
	return "/o2o/";
}
```

2.在需要加载图片页面对应的js中加载图片的地方添加getContextPath前缀就好了

![](/uploads/181227springboot/9.png)

注意主要忘了在对应的html中引入common.js。
至此，我们的springboot项目可以正常运行了。








