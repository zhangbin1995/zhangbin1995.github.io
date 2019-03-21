---
title: jquery基础
date: 2017-11-10 19:42:28
tags:
	- 前端
categories:
	- 前端
---

在jQuery中 `$()` 方法等价于 `jQuery()` 方法，前者比较常用，是后者的简写。一般只有在`$()` 与其他语言冲突时才会使用`jQuery()` 方法。

**#id 选择器**

jquery能使用CSS选择器来操作网页中的标签元素。如果你想要通过一个id号去查找一个元素，就可以使用如下格式的选择器：

```
$("#my_id")
其中#my_id表示根据id选择器获取页面中指定的标签元素，且返回唯一一个元素。
```

**element 选择器**

```
$(“element”)
其中element就是元素的名称  例如：$("div")
```

**.class 选择器**

```
$(“.class”)
其中.class参数表示元素的CSS类别(类选择器)名称。
```

**\* 选择器**

`$(“*”)`
选择器中的参数就一个“*”，既没有“#”号，也没有“.”号。 由于该选择器的特殊性，它常与其他元素组合使用，表示获取其他元素中的全部子元素。
`$("form *").attr("disabled", "true")`;

**sele1,sele2,seleN选择器**

`$(“sele1,sele2,seleN”)`
其中参数sele1、sele2到seleN为有效选择器，每个选择器之间用“，”号隔开，它们可以是之前提及的各种类型选择器，如`$(“#id”)、$(“.class”)、$(“selector”)`选择器等。 
`$(".red,.green").html("hi,我们的样子很美哦!");`

**ance desc选择器**

```
$("ance desc")
```
其中ance desc是使用空格隔开的两个参数。ance参数（ancestor祖先的简写）表示父元素；desc参数（descendant后代的简写）表示后代元素，即包括子元素、孙元素等等。两个参数都可以通过选择器来获取。比如家族姓氏“div”，家族几代人里，都有名字里带“span”的，就可以用这个ance desc选择器把这几个人给定位出来。

**parent > child选择器**

与上一节介绍的ance desc选择器相比，`parent > child`选择器的范围要小些，它所选择的目标是子集元素，相当于一个家庭中的子辈们，但不包括孙辈，它的调用格式如下：

```
$(“parent > child”)
child参数获取的元素都是parent选择器的子元素，它们之间通过“>”符号来表示一种层次关系。
```

**prev + next选择器**

俗话说“远亲不如近邻”，而通过prev + next选择器就可以查找与“prev”元素紧邻的下一个“next”元素，格式如下：

```
$(“prev + next”)
```
其中参数prev为任何有效的选择器，参数“next”为另外一个有效选择器，它们之间的“+”表示一种上下的层次关系，也就是说，“prev”元素最紧邻的下一个元素由“next”选择器返回的并且只返回唯的一个元素。

**prev ~ siblings选择器**

与上一节中介绍的`prev + next`层次选择器相同，`prev ~ siblings`选择器也是查找`prev` 元素之后的相邻元素，但前者只获取第一个相邻的元素，而后者则获取prev 元素后面全部相邻的元素，它的调用格式如下：

```
$(“prev ~ siblings”)
```
其中参数prev与siblings两者之间通过“~”符号形成一种层次相邻的关系，表明siblings选择器获取的元素都是prev元素之后的同辈元素。

**:first过滤选择器**

```
$(“li:first”)   $("li:last")
        <ol>
            <li>葡萄</li>
            <li>香蕉</li>
            <li>橘子</li>
            <li>西瓜</li>
            <li>苹果</li>
        </ol>

        <script type="text/javascript">
            $("li:last").css("background-color", "red");
        </script>
```

**:eq(index)过滤选择器**

如果想从一组标签元素数组中，灵活选择任意的一个标签元素，我们可以使用

```
:eq(index)
```
其中参数`index`表示索引号（即：一个整数），它从0开始，如果index的值为3，表示选择的是第4个元素

**:contains(text)过滤选择器**

有时候我们可能希望按照文本内容来查找一个或多个元素，那么使用`:contains(text)`选择器会更加方便， 它的功能是选择包含指定字符串的全部元素，它通常与其他元素结合使用，获取包含`text`字符串内容的全部元素对象。其中参数text表示页面中的文字。

**:has(selector)过滤选择器**

还可以使用包含的元素名称来过滤，`:has(selector)`过滤选择器的功能是获取选择器中包含指定元素名称的全部元素，其中selector参数就是包含的元素名称，是被包含元素。

例如：获取指定包含某个元素名的全部<li>元素，并改变它们显示文字的颜色，如下图所示：

![](/uploads/171110jquery/1.png)

**:hidden过滤选择器**

`:hidden`过滤选择器的功能是获取全部不可见的元素，这些不可见的元素中包括`type`属性值为`hidden`的元素。

例如，调用`:hidden`选择器获取不可见的`<p>`元素，并将该元素的内容显示在`<div>`元素中，如下图所示：

![](/uploads/171110jquery/2.png)

**:visible过滤选择器**

与上一节的`:hidden`过滤选择器相反，`:visible`过滤选择器获取的是全部可见的元素，也就是说，只要不将元素的`display`属性值设置为`none`，那么，都可以通过该选择器获取。

**[attribute=value]属性选择器**

属性作为DOM元素的一个重要特征，也可以用于选择器中，从本节开始将介绍通过元素属性获取元素的选择器，`[attribute=value]`属性选择器的功能是获取与属性名和属性值完全相同的全部元素，其中`[]`是专用于属性选择器的括号符，参数`attribute`表示属性名称，`value`参数表示属性值。

例如，使用`[attribute=value]`属性选择器，获取指定属性名和对应值的全部`<li>`元素，并设置它们显示的文字颜色，如图所示：

![](/uploads/171110jquery/3.png)

**[attribute!=value]属性选择器**

`$("li[title!='我最爱']")`   <--- 上面改成


**[attribute\*=value]属性选择器**

介绍一个功能更为强大的属性选择器`[attribute*=value]`，它可以获取属性值中包含指定内容的全部元素，其中[]是专用于属性选择器的括号符，参数attribute表示属性名称，value参数表示对应的属性值。

例如，使用`[attribute*=value]`属性选择器，获取属性值中包含某一指定内容的全部`<li>`元素，并设置它们显示的文字颜色，如下图所示：

![](/uploads/171110jquery/4.png)

**:first-child子元素过滤选择器**

通过上面章节的学习，我们知道使用`:first`过滤选择器可以获取指定父元素中的首个子元素，但该选择器返回的只有一个元素，并不是一个集合，而使用`:first-child`子元素过滤选择器则可以获取每个父元素中返回的首个子元素，它是一个集合，常用多个集合数据的选择处理。

如下图，如果想把页面中每个`ul`中的第一个`li`获取到，并改变其颜色。则可以使用
`: first-child`

![](/uploads/171110jquery/5.png)

**:input表单选择器**

如何获取表单全部元素？`:input`表单选择器可以实现，它的功能是返回全部的表单元素，不仅包括所有`<input>`标记的表单元素，而且还包括`<textarea>`、`<select>` 和 `<button>`标记的表单元素，因此，它选择的表单元素是最广的。

```
$("#frmTest :input").addClass("bg_blue");
```

**:text表单文本选择器**

`:text`表单文本选择器可以获取表单中全部单行的文本输入框元素，单行的文本输入框就像一个不换行的字条工具，使用非常广泛。

通过`:text`表单选择器只获取单行的文本输入框元素，对于`<textarea>`区域文本、按钮元素无效.

**:password表单密码选择器**

**:radio单选按钮选择器**

**:checkbox复选框选择器**

**:submit提交按钮选择器**

**:image图像域选择器**

当一个`<input>`元素的`type`属性值设为`image`时，该元素就是一个图像域，使用`:image`选择器可以快速获取该类全部元素。

**:button表单按钮选择器**

**:checked选中状态选择器**

有一些元素存在选中状态，如复选框、单选按钮元素，选中时`checked`属性值为`checked`，调用`:checked`可以获取处于选中状态的全部元素

**:selected选中状态选择器**

与`:checked`选择器相比，`:selected`选择器只能获取`<select>`下拉列表框中全部处于选中状态的`<option>`选项元素。

**使用attr()方法控制元素的属性**

`attr()`方法的作用是设置或者返回元素的属性，其中attr(属性名)格式是获取元素属性名的值，attr(属性名，属性值)格式则是设置元素属性名的值。

```
        <a href="http://127.0.0.1" id="a1">点我就变</a>
        <div>我改变后的地址是：<span id="tip"></span></div>

        <script type="text/javascript">
            $("#a1").attr("href" , "www.imooc.com");   //赋值
            var $url = $("#a1").attr("href");          //取值
            $("#tip").html($url);
        </script>
```

**操作元素的内容**

使用`html()`和`text()`方法操作元素的内容，当两个方法的参数为空时，表示获取该元素的内容，而如果方法中包含参数，则表示将参数值设置为元素内容。

**操作元素的样式**

通过`addClass()`和`css()`方法可以方便地操作元素中的样式，前者括号中的参数为增加元素的样式名称，后者直接将样式的属性内容写在括号中。

```
$("#content").css({"background-color":"red","color":"white"});
```

**移除属性和样式**

使用`removeAttr(name)`和`removeClass(class)`分别可以实现移除元素的属性和样式的功能，前者方法中参数表示移除属性名，后者方法中参数则表示移除的样式名

**使用append()方法向元素内追加内容**

`append(content)`方法的功能是向指定的元素中追加内容，被追加的`content`参数，可以是字符、HTML元素标记，还可以是一个返回字符串内容的函数。

```
    <body>
        <h3>append()方法追加内容</h3>

        <script type="text/javascript">
            function rethtml() {
                var $html = "<div id='test' title='hi'>我是调用函数创建的</div>"
                return $html;
            }
            $("body").append(rethtml());
        </script>
    </body>
```

**使用appendTo()方法向被选元素内插入内容**

`appendTo()`方法也可以向指定的元素内插入内容，它的使用格式是：

```
$(content).appendTo(selector)
参数content表示需要插入的内容，参数selector表示被选的元素，即把content内容插入selector元素内，默认是在尾部。
    <body>
        <h3>appendTo()方法插入内容</h3>
        <div>
            <span class="green">小乌龟</span>
        </div>

        <script type="text/javascript">
            var $html = "<span class='red'>小青蛙</span>"
            $($html).appendTo("div");
        </script>
    </body>
```

**使用before()和after()在元素前后插入内容**

使用`before()`和`after()`方法可以在元素的前后插入内容，它们分别表示在整个元素的前面和后面插入指定的元素或内容，调用格式分别为：
`$(selector).before(content)`和`$(selector).after(content)`
其中参数content表示插入的内容，该内容可以是元素或HTML字符串。

**使用clone()方法复制元素**

调用`clone()`方法可以生成一个被选元素的副本，即复制了一个被选元素，包含它的节点、文本和属性，它的调用格式为：

```
$(selector).clone()
其中参数selector可以是一个元素或HTML内容。
    <body>
        <h3>使用clone()方法复制元素</h3>
        <span class="red" title="hi">我是美猴王</span>

        <script type="text/javascript">
            $("body").append($(".red").clone());
        </script>
    </body>
```

**替换内容**

`replaceWith()`和`replaceAll()`方法都可以用于替换元素或元素中的内容，但它们调用时，内容和被替换元素所在的位置不同，分别为如下所示：

```
$(selector).replaceWith(content)和$(content).replaceAll(selector)
参数selector为被替换的元素，content为替换的内容。
    <body>
        <h3>使用replaceAll()方法替换元素内容</h3>
        <span class="green" title="hi">我是屌丝</span>

        <script type="text/javascript">
            var $html = "<span class='red' title='hi'>我是土豪</span>";
            $($html).replaceAll($(".green"));
        </script>
    </body>
```

**使用wrap()和wrapInner()方法包裹元素和内容**

```
    <body>
        <h3>使用wrapInner()方法包裹元素</h3>
        <span class="red" title='hi'>我的身体有点歪</span>

        <script type="text/javascript">
            $(".red").wrapInner("<i></i>");
        </script>
    </body>
```

**使用each()方法遍历元素**

使用`each()`方法可以遍历指定的元素集合，在遍历时，通过回调函数返回遍历元素的序列号，它的调用格式为：

```
$(selector).each(function(index))
参数function为遍历时的回调函数，index为遍历元素的序列号，它从0开始。
    <body>
        <h3>使用each()方法遍历元素</h3>
        <span class="green">香蕉</span>
        <span class="green">桃子</span>
        <span class="green">葡萄</span>
        <span class="green">荔枝</span>

        <script type="text/javascript">
            $("span").each(function(index){
                if(index==2){
                    $(this).addClass("red");
                }
                })
        </script>
    </body>
```

**使用remove()和empty()方法删除元素**

`remove()`方法删除所选元素本身和子元素，该方法可以通过添加过滤参数指定需要删除的某些元素，而`empty()`方法则只删除所选元素的子元素。

```
    <body>
        <h3>使用empty()方法删除元素</h3>
        <span class="green">香蕉</span>
        <span class="green">桃子</span>
        <span class="green">葡萄</span>
        <span class="green">荔枝</span>

        <script type="text/javascript">
            //$("span").remove(".green");
            $("span").empty();
    </script>
    </body>
```

**页面加载时触发ready()事件**

`ready()`事件类似于`onLoad()`事件，但前者只要页面的DOM结构加载后便触发，而后者必须在页面全部元素加载成功才触发，`ready()`可以写多个，按顺序执行。此外，下列写法是相等的：

```
$(document).ready(function(){})等价于$(function(){});
```

**使用bind()方法绑定元素的事件**

`bind()`方法绑定元素的事件非常方便，绑定前，需要知道被绑定的元素名，绑定的事件名称，事件中执行的函数内容就可以，它的绑定格式如下：

```
$(selector).bind(event,[data] function)
参数event为事件名称，多个事件名称用空格隔开，function为事件执行的函数。
```

**使用hover()方法切换事件**

`hover()`方法的功能是当鼠标移到所选元素上时，执行方法中的第一个函数，鼠标移出时，执行方法中的第二个函数，实现事件的切实效果，调用格式如下：

```
$(selector).hover(over，out);
over参数为移到所选元素上触发的函数，out参数为移出元素时触发的函数。
    <body>
        <h3>hover()方法切换事件</h3>
        <div>别走！你就是土豪</div>

        <script type="text/javascript">
            $(function () {
                $("div").hover(
                function () {
                    $(this).addClass("orange");
                },
                function () {
                    $(this).removeClass("orange")
                })
            });
        </script>
    </body>
```

**使用toggle()方法绑定多个函数**

`toggle()`方法可以在元素的click事件中绑定两个或两个以上的函数，同时，它还可以实现元素的隐藏与显示的切换，绑定多个函数的调用格式如下：

```
$(selector).toggle(fun1(),fun2(),funN(),...)
```
其中，fun1，fun2就是多个函数的名称
例如，使用`toggle()`方法，当每次点击`<div>`元素时，显示不同内容，如下图所示：

![](/uploads/171110jquery/6.png)

**用作隐藏和显示的切换用法：**

```
	<body>
        <h3>toggle()方法绑定多个函数</h3>
        <input id="btntest" type="button" value="点一下我" />
        <div>我是动态显示的</div>

        <script type="text/javascript">
            $(function () {
                $("#btntest").bind("click", function () {
                    $("div").toggle();
                })
            });
        </script>
    </body>
```

**使用unbind()方法移除元素绑定的事件**

`unbind()`方法可以移除元素已绑定的事件，它的调用格式如下：

```
$(selector).unbind(event,fun)
```
其中参数event表示需要移除的事件名称，多个事件名用空格隔开，fun参数为事件执行时调用的函数名称。
例如，点击按钮时，使用`unbind()`方法移除`<div>`元素中已绑定的`dblclick`事件，如下图所示：

![](/uploads/171110jquery/7.png)

**使用one()方法绑定元素的一次性事件**


