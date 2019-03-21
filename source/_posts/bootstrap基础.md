---
title: bootstrap基础
date: 2017-11-08 15:53:17
tags:
	- 前端
categories:
	- 前端
---

**基本的HTML模板**

bootstrap模板为使IE6、7、8版本（IE9以下版本）浏览器兼容html5新增的标签，引入下面代码文件即可。

```
<script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
```

同理为使IE6、7、8版本浏览器兼容css3样式，引入下面代码：

```
<script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
```

**按钮（嵌套分组）**

很多时候，我们常把下拉菜单和普通的按钮组排列在一起，实现类似于导航菜单的效果。如下所示：

![](/uploads/171108bootstrap/1.png)

使用的时候，只需要把当初制作下拉菜单的“dropdown”的容器换成“btn-group”，并且和普通的按钮放在同一级。如下所示：

```
<div class="btn-group">
<button class="btnbtn-default" type="button">首页</button>
<button class="btnbtn-default" type="button">产品展示</button>
<button class="btnbtn-default" type="button">案例分析</button>
<button class="btnbtn-default" type="button">联系我们</button>
<div class="btn-group">
   <button class="btnbtn-default dropdown-toggle" data-toggle="dropdown" type="button">关于我们<span class="caret"></span></button>
   <ul class="dropdown-menu">
         <li><a href="##">公司简介</a></li>
         <li><a href="##">企业文化</a></li>
         <li><a href="##">组织结构</a></li>
         <li><a href="##">客服服务</a></li>
    </ul>
</div>
</div>
```

**按钮（等分按钮）**

等分按钮的效果在移动端上特别的实用。整个按钮组宽度是容器的100%，而按钮组里面的每个按钮平分整个容器宽度。例如，如果你按钮组里面有五个按钮，那么每个按钮是20%的宽度，如果有四个按钮，那么每个按钮是25%宽度，以此类推。

等分按钮也常被称为是自适应分组按钮，其实现方法也非常的简单，只需要在按钮组`“btn-group”`上追加一个`“btn-group-justified”`类名，如下所示：

```
<div class="btn-wrap">
	<div class="btn-group btn-group-justified">
	  <a class="btnbtn-default" href="#">首页</a>
	  <a class="btnbtn-default" href="#">产品展示</a>
	  <a class="btnbtn-default" href="#">案例分析</a>
	  <a class="btnbtn-default" href="#">联系我们</a>
	</div>
</div>
```
运行效果如下：

![](/uploads/171108bootstrap/2.png)

实现原理非常简单，把`“btn-group-justified”`模拟成表格`（display:table）`，而且把里面的按钮模拟成表格单元格`（display:table-cell）`。具体样式代码如下：

```
/*源码请查看bootstrap.css文件第3277行～第3291行*/
.btn-group-justified {
  display: table;
  width: 100%;
  table-layout: fixed;
  border-collapse: separate;
}
.btn-group-justified > .btn,
.btn-group-justified > .btn-group {
  display: table-cell;
  float: none;
  width: 1%;
}
.btn-group-justified > .btn-group .btn {
  width: 100%;
}
```

特别声明：在制作等分按钮组时，请尽量使用`<a>`标签元素来制作按钮，因为使用`<button>`标签元素时，使用`display:table`在部分浏览器下支持并不友好。

**固定导航条**

很多情况之一，设计师希望导航条固定在浏览器顶部或底部，这种固定式导航条的应用在移动端开发中更为常见。Bootstrap框架提供了两种固定导航条的方式：

> * .navbar-fixed-top：导航条固定在浏览器窗口顶部
> * .navbar-fixed-bottom：导航条固定在浏览器窗口底部

使用方法很简单，只需要在制作导航条最外部容器navbar上追加对应的类名即可：

```
<div class="navbar navbar-default navbar-fixed-top" role="navigation">
　…
</div>
<div class="content">我是内容</div>
<div class="navbar navbar-default navbar-fixed-bottom" role="navigation">
　…
</div>
```

实现原理：
实现原理很简单，就是在`navbar-fixed-top`和`navbar-fixed-bottom`使用了`position：fixed`属性，并且设置`navbar-fixed-top`的top值为0,而`navbar-fixed-bottom`的bottom值为0。

**分页导航（带页码的分页导航）**
分页导航几乎在哪个网站都可见。好的分页导航能给用户带来更好的用户体验。在Bootstrap框架中提供了两种分页导航：
> * 带页码的分页导航
> * 带翻页的分页导航

**带页码的分页导航**

带页码的分页导航，可能是最常见的一种分页导航，特别是在列表页内容超多的时候，会给用户提供分页的导航方式。在Bootstrap框架为开发者提供不同的版本：
> * LESS版本：对应的源文件pagination.less
> * Sass版本：对应的源文件_pagination.scss
> * 编译后版本：对应bootstrap.css文件第4130行～第4222行

**使用方法：**

平时很多同学喜欢用`div>a`和`div>span`结构来制作带页码的分页导航。不过，在`Bootstrap`框架中使用的是`ul>li>a`这样的结构，在`ul`标签上加入`pagination`方法：

```
<ul class="pagination">
   <li><a href="#">&laquo;</a></li>
   <li><a href="#">1</a></li>
   <li><a href="#">2</a></li>
   <li><a href="#">3</a></li>
   <li><a href="#">4</a></li>
   <li><a href="#">5</a></li>
   <li><a href="#">&raquo;</a></li>
</ul>
```

运行效果：

![](/uploads/171108bootstrap/3.png)

**实现原理：**

从效果中可以看出，当前状态页码会高亮显示，而且不能点击。而最后一页是禁用状态，也不能点击。实现样式：

```
/*bootstrap.css文件第4170行~第4192行*/
.pagination> .active > a,
.pagination> .active > span,
.pagination> .active >a:hover,
.pagination> .active >span:hover,
.pagination> .active >a:focus,
.pagination> .active >span:focus {
z-index: 2;
color: #fff;
cursor: default;
background-color: #428bca;
border-color: #428bca;
}
.pagination> .disabled > span,
.pagination> .disabled >span:hover,
.pagination> .disabled >span:focus,
.pagination> .disabled > a,
.pagination> .disabled >a:hover,
.pagination> .disabled >a:focus {
color: #999;
cursor: not-allowed;
background-color: #fff;
border-color: #ddd;
}
```

**注意：**要禁用当前状态和禁用状态不能点击，我们还要依靠js来实现，或者将这两状态下的a标签换成span标签。

**大小设置：**

在Bootstrap框架中，也可以通过几个不同的情况来设置其大小。类似于按钮一样：
> * 通过“pagination-lg”让分页导航变大；
> * 通过“pagination-sm”让分页导航变小：

```
<ul class="pagination pagination-lg">
 …
</ul>
<ul class="pagination">
 …
</ul>
<ul class="pagination pagination-sm">
 …
</ul>
```

**大小设置实现原理：**

其实就是通增加相应的`padding`大小、`font-size`大小和圆角大小，源码查看`bootstrap.css`文件第4193行～第4222行.

**导入JavaScript插件**

Bootstrap除了包含丰富的Web组件之外，如前面介绍的下拉菜单、按钮组、导航、分页等。他还包括一些JavaScript的插件。

Bootstrap的JavaScript插件可以单独导入到页面中，也可以一次性导入到页面中。因为在Bootstrap中的JavaScript插件都是依赖于jQuery库，所以不论是单独导入还一次性导入之前必须先导入jQuery库。

**一次性导入：**

Bootstrap提供了一个单一的文件，这个文件包含了Bootstrap的所有JavaScript插件，即bootstrap.js（压缩版本：bootstrap.min.js）。

**具体使用如下:**

```
<!—导入jQuery版本库，因为Bootstrap的JavaScript插件依赖于jQuery -->
<script src="http://libs.baidu.com/jquery/1.9.0/jquery.js"></script>
<!—- 一次性导入所有Bootstrap的JavaScript插件（压缩版本） -->
<script src="js/bootstrap.min.js"></script>
```

特别声明：jQuery版本库也可以加载你本地的jQuery版本。

**单独导入：**

为方便单独导入特效文件，Bootstrap V3.2中提供了12种JavaScript插件，他们分别是：
> * 动画过渡（Transitions）:对应的插件文件“transition.js”
> * 模态弹窗（Modal）:对应的插件文件“modal.js”
> * 下拉菜单（Dropdown）：对应的插件文件“dropdown.js”
> * 滚动侦测（Scrollspy）：对应的插件文件“scrollspy.js”
> * 选项卡（Tab）：对应的插件文件“tab.js”
> * 提示框（Tooltips）：对应的插件文件“tooltop.js”
> * 弹出框（Popover）：对应的插件文件“popover.js”
> * 警告框（Alert）：对应的插件文件“alert.js”
> * 按钮（Buttons）：对应的插件文件“button.js”
> * 折叠/手风琴（Collapse）：对应的插件文件“collapse.js”
> * 图片轮播Carousel：对应的插件文件“carousel.js”
> * 自动定位浮标Affix：对应的插件文件“affix.js”

上述单独插件的下载可到github去下载

[https://github.com/twbs/bootstrap](https://github.com/twbs/bootstrap)









