---
title: SpringBoot验证码Kaptcha的使用
date: 2018-12-28 20:01:51
tags:
	- java
	- springboot
categories:
	- java
---

在ssm中kapcha的配置主要写在web.xml中，而springboot中并没有使用web.xml文件，
所以我们要重新总结下验证码在springboot中的使用。
首先我们要将和web.xml中的Kaptcha的相关配置写入到application.properties中

```
#Kaptcha的配置
kaptcha.border=no
kaptcha.textproducer.font.color=red
kaptcha.textproducer.font.size=43
kaptcha.image.width=135
kaptcha.image.height=50
kaptcha.textproducer.char.string=ACDEFHKPRSTWX345679
kaptcha.noise.color=black
kaptcha.textproducer.char.length=4
kaptcha.textproducer.font.names=Arial
```

再在MvcConfiguration类中进行配置

```
@Value("${kaptcha.border}")
	private String border;
	@Value("${kaptcha.textproducer.font.color}")
	private String fcolor;
	@Value("${kaptcha.image.width}")
	private String width;
	@Value("${kaptcha.textproducer.char.string}")
	private String cString;
	@Value("${kaptcha.image.height}")
	private String height;
	@Value("${kaptcha.textproducer.font.size}")
	private String fsize;
	@Value("${kaptcha.noise.color}")
	private String nColor;
	@Value("${kaptcha.textproducer.char.length}")
	private String clength;
	@Value("${kaptcha.textproducer.font.names}")
	private String fnames;
	
	/**
	 * 由于web.xml不生效了，需要在这里配置Kaptcha验证码Servlet
	 * @return
	 * @throws ServletException
	 */
	@Bean
	public ServletRegistrationBean servletRegistrationBean() throws ServletException{
                //只要在前端访问Kaptcha这个路由就会启动这个servlet
		ServletRegistrationBean servlet = new ServletRegistrationBean(new KaptchaServlet(),"/Kaptcha");
		servlet.addInitParameter("keptcha.border", border);//无边框
		servlet.addInitParameter("kaptcha.textproducer.font.color", fcolor);//字体颜色
		servlet.addInitParameter("kaptcha.image.width", width);//图片宽度
		servlet.addInitParameter("kaptcha.textproducer.char.string", cString);//使用哪些字符生成验证码
		servlet.addInitParameter("kaptcha.image.height", height);//图片高度
		servlet.addInitParameter("kaptcha.textproducer.font.size", fsize);//字体大小
		servlet.addInitParameter("kaptcha.noise.color", nColor);//干扰线的颜色
		servlet.addInitParameter("kaptcha.textproducer.char.length", clength);//字符个数
		servlet.addInitParameter("kaptcha.textproducer.font.names", fnames);//字体
		return servlet;
	}
```

当然还有别忘了pom里面jar包的引入。

```
	<!-- 验证码相关 -->
	<!-- https://mvnrepository.com/artifact/com.github.penggle/kaptcha -->
	<dependency>
		<groupId>com.github.penggle</groupId>
		<artifactId>kaptcha</artifactId>
		<version>2.3.2</version>
	</dependency>
```