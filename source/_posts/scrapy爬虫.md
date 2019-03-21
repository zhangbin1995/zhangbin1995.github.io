---
title: scrapy爬虫
date: 2018-05-10 10:21:17
tags:
	- python
categories:
	- python
---

**技术选型**

scrapy vs requests + beautifulsoup
> * 1.requests和beautifulsoup都是库，scrapy是框架
> * 2.scrapy框架中可以加入requests和beautifulsoup
> * 3.scrapy基于twisted，性能是最大的优势
> * 4.scrapy方便扩展，提供了很多内置的功能
> * 5.scrapy内置的css和xpath selector非常方便，beautifulsoup最大的缺点就是慢

**网页分类**
> * 1.静态网页
> * 2.动态网页
> * 3.webservice(restapi)

**爬虫作用**
> * 1.搜索引擎---百度、google、垂直领域搜索引擎
> * 2.推荐引擎---今日头条
> * 3.机器学习的数据样本
> * 4.数据分析（如金融数据分析）、舆情分析等

**正则表达式**

```
^[0-9]+abc$
^为匹配输入字符串的开始位置
[0-9]+匹配多个数字，[0-9]匹配单个数字，+匹配一个或者多个
abc$匹配字母abc并以abc结尾，$为匹配输入字符串的结束位置
```

实例

匹配以数字开头，并以abc结尾的字符串：

```
var str = "123abc";
var str1 = "12323123abc";
var str2 = "12323123abcabc";
var patt1 = /^[0-9]+abc$/;
document.write(str.match(patt1));
输出结果
123abc
12323123abc
null
```

**为什么使用正则表达式**

典型的搜索和替换操作要求提供与预期的搜索结果匹配的确切文本。虽然这种技术对于对静态文本执行简单搜索和替换任务可能已经足够了，但它缺乏灵活性，若采用这种方法搜索动态文本，即使不是不可能，至少也会变得很困难。

通过使用正则表达式，可以：
> * 测试字符串的模式。
例如，可以测试输入字符串，以查看字符串是否出现电话号码模式或者信用卡号码模式。这称为数据验证。
>  *替换文本
可以使用正则表达式来识别文档中的特定文本，完全删除该文本或者用其他文本替换它。
> * 基于模式匹配从字符串中提取子字符串
> * 可以查找文档内或输入域内特定的文本   

```
# _*_ coding: utf-8 _*_
import re

line = "bobby123"
reges_str = "^b.*"
if re.match(reges_str,line):
    print("yes")
else:
    print("no")

# ^b.* :以b开头的后面任意字符任意多个     .任意字符    *任意多个
```

```
# _*_ coding: utf-8 _*_
import re

line = "booooooobby123"
regex_str = ".*(b.*b).*"
match_obj = re.match(regex_str,line)
if match_obj:
    print(match_obj.group(1))
```

输出结果：bb
因为是贪婪匹配 从后往前匹配的   booooooobby123

```
# _*_ coding: utf-8 _*_
import re

line = "booooooobby123"
regex_str = ".*?(b.*b).*"
match_obj = re.match(regex_str,line)
if match_obj:
    print(match_obj.group(1))
```

输出结果：booooooob

贪婪匹配：在满足匹配时，匹配尽可能长的字符串，默认情况下，采用贪婪匹配

非贪婪匹配：在满足匹配时，匹配尽可能短的字符串，使用?来表示非贪婪匹配

特殊字符
> * 1) ^ $ * ? + {2} {2,} {2,5} |
> * 2) []：字符集合		 [^]：负值字符集合，匹配未包含的		 [a-z]      
> * 3) \s \S \w \W     空格    a-zA-Z0-9_
> * 4) `[\u4E00-\u9FA5] () \d`     中文    数字

```
# _*_ coding: utf-8 _*_
import re

##提取生日的小demo
line = "XXX出生于2001年6月1日"
#line = "XXX出生于2001/6/1"
#line = "XXX出生于2001-6-1"
#line = "XXX出生于2001-06-01"
#line = "XXX出生于2001-06"
regex_str = ".*出生于(\d{4}[年/-]\d{1,2}([月/-]\d{1,2}|[月/-]$|$))"
match_obj = re.match(regex_str,line)
if match_obj:
    print(match_obj.group(1))
```

**深度优先和广度优先**

深度优先过程

```
def depth_tree(tree_node):
    if tree_node is not None:
        print(tree_node._data)
        if tree_node._left is not None:
            return depth_tree(tree_node._left)
        if tree_node._right is not None:
            return depth_tree(tree_node._right)
```

广度优先过程
```
def level_queue(root):
    """利用队列实现树的广度优先遍历"""
    if root is None:
        return
    my_queue = []
    node = root
    my_queue.append(node)
    while my_queue:
        node = my_queue.pop(0)
        print(node.elem)
        if node.lchild is not None:
            my_queue.append(node.lchild)
        if node.rchild is not None:
            my_queue.append(node.rchild)
```

爬虫去重策略

> * 1.将访问过的url保存到数据库中
> * 2.将访问过的url保存到set中，只需要o(1)的代价就可以查询url
因为存在内存中，所以对内存要求较高，例如一亿个url占用空间
`100000000*2byte*50`个字符/1024/1024/1024=9G
> * 3.url经过md5等方法哈希后保存到set中 （scrapy用的就是这个）
> * 4.用bitmap方法，将访问过的url通过hash函数映射到某一位
> * 5.bloomfilter方法对bitmap进行改进，多重hash函数降低冲突

字符串编码
> * 1.计算机只能处理数字，文本转换为数字才能处理。计算机中8个bit作为一个字节，所以一个字节能表示最大的数字就是255
> * 2.计算机是美国人发明的，所以一个字节可以表示所有字符了，所以ASCII（一个字节）编码就成为美国人的标准编码
> * 3.但是ASCII处理中文明显是不够的，中文不止255个汉字，所以中国制定了GB2312编码，用两个字节表示一个汉字。GB2312还把ASCII包含进去了，同理，日文，韩文等等上百个国家为了解决这个问题就都发展了一套字节的编码，标准就越来越多，如果出现多种语言混合显示就一定会出现乱码
> * 4.于是unicode出现了，将所有语言统一到一套编码里
> * 5. 看一下ASCII和unicode编码：
	1）字母A用ASCII编码十进制是65，二进制0100 0001
	2）汉字“中”已近超出了ASCII编码的范围，用unicode编码是20013 二进制是01001110 00101101
	3）A用unicode编码只需要前面补0二进制是00000000 0100 0001
> * 6.乱码问题解决了，但是如果内容全是英文，unicode编码比ASCII需要多一倍的存储空间，同时如果传输需要多一倍的传输。
> * 7.所以出现了可变长的编程“utf-8”，把英文边长一个字节，汉字3个字节。特别生僻的变成4-6字节，如果传输大量的英文，utf8作用就很明显了

安装virtualenv步骤
[https://www.cnblogs.com/anpengapple/p/6430022.html](https://www.cnblogs.com/anpengapple/p/6430022.html)

指定python版本的virtualenv环境

```
mkvirtualenv -p python3 virtualtest1
```


scrapy爬取伯乐在线

```
mkvirtualenv article_spider
pip install -i https://pypi.douban.com/simple/ scrapy

workon  查看所有虚拟环境
workon article_spider   选取（切换）目标环境
scrapy startproject ArticleSpider  新建scrapy项目
然后去pcharm里打开这个项目  ArticleSpider
cd ArticleSpider
scrapy genspider jobbole blog.jobbole.com
```

启动爬虫时 要将settings.py中的 ROBOTSTXT_OBEY = False 注释放开

**xpath**
1.xpath简介
> * ①xpath使用路径表达式在xml和html中进行导航
> * ②xpath包含标准函数库
> * ③xpath是一个w3c的标准

xpath节点关系
> * 父节点 子节点 同胞节点 先辈节点 后代节点

2.xpath术语

3.xpath语法

```
article	选取所有article元素的所有子节点
/articel	选取根节点article
article/a	选取所有属于article的子元素的a元素
//div	选取所有div子元素（不论出现在文档任何地方）
article//div选取所有属于article元素的后代的div元素，不管他出现在article之下的任何位置
//@class	选取所有名为class的属性

/article/div[1]		选取属于article子元素的第一个div元素
/article/div[last()]	选取属于article子元素的最后一个div元素
/article/div[last()-1]	选取属于article子元素的倒数第二个div元素
//div[@lang]		选取所有拥有lang属性的div元素
//div[@lang='eng']	选取所有lang属性为eng的div元素

/div/*			选取属于div元素的所有子节点
//*				选取所有元素
//div[@*]			选取所有带属性的title元素
/div/a | //div/p	选取所有div元素的a和p元素
//span | //ul		选取文档中的span和ul元素
article/div/p | //span选取所有属于article元素的div元素的p元素 以及文档中所有的span元素

scrapy shell http://blog.jobbole.com/110287
命令行对一个url进行调试  进入命令行模式后
title = response.xpath("//div[@class='entry-header']/h1/text()")
title
#输出 [<Selector xpath="//div[@class='entry-header']/h1/text()" data='2016 腾讯软件开发面试题（部分）'>]
title.extract()
['2016 腾讯软件开发面试题（部分）']
title.extract()[0]
'2016 腾讯软件开发面试题（部分）'

将日期里面的 . 和空格去掉
date = response.xpath(xxxxxxxx.extract()[0].strip().replace(" . "," ").strip())
```

css选择器

```
*			选择所有节点
#container	选择id为container的节点
.container	选取所有class包含container的节点
li a			选取所有li下的所有a节点
ul+p		选取ul后面的第一个p元素
div#container>ul	选取id为container的div的第一个ul子元素 
ul ~ p		选取与ul相邻的所有p元素
a[title]		 选取所有有title属性的a元素
a[href="http://jobbole.com"]	选取所有href属性为jobbole.com值得元素
a[href*="jobbole"]	选取所有href属性包含jobbole的a元素
a[href^="http"]	选取所有href属性值以http开头的a元素
a[href$=".jpg"]	选取所有href属性值以.jpg结尾的a元素
input[type=radio]:checked	选择选中的radio的元素
div:not(#container)	选取所有id非container的div属性
li:nth-child(3)		选取第三个li元素
tr:nth-child(2n)	第偶数个tr

提取文本 ::text
response.css(".entry-header h1::text").extract()

scrapy shell blog.jobbole.com/all-posts/
response.css("#archive .floated-thumb .post-thumb a::attr(href)").extract()
Out[1]: 
['http://blog.jobbole.com/113771/',
 'http://blog.jobbole.com/113768/',
 'http://blog.jobbole.com/112048/',
 'http://blog.jobbole.com/113760/',
 'http://blog.jobbole.com/113740/',
 'http://blog.jobbole.com/113631/',
 'http://blog.jobbole.com/113737/',
 'http://blog.jobbole.com/113735/',
 'http://blog.jobbole.com/113707/',
 'http://blog.jobbole.com/113728/',
 'http://blog.jobbole.com/113722/',
 'http://blog.jobbole.com/113726/',
 'http://blog.jobbole.com/113719/',
 'http://blog.jobbole.com/113716/',
 'http://blog.jobbole.com/113710/',
 'http://blog.jobbole.com/113713/',
 'http://blog.jobbole.com/113705/',
 'http://blog.jobbole.com/113699/',
 'http://blog.jobbole.com/113696/',
 'http://blog.jobbole.com/113692/']

next_urls = response.css(".next.page-numbers")
next_urls = response.css(".next .page-numbers")
两个class next和page-numbers中如果没有空格说明是一个模块里的两个class属性 同级的
如果有空格 说明是下级关系 
<div class="next page-numbers"></div>
<div class="next">
<div class="page-numbers"></div>
</div>

"""
1. 获取文章列表页中的文章url并交给scrapy下载后并进行解析
2. 获取下一页的url并交给scrapy进行下载， 下载完成后交给parse
"""
#解析列表页中的所有文章url并交给scrapy下载后并进行解析
if response.status == 404:
    self.fail_urls.append(response.url)
    self.crawler.stats.inc_value("failed_url")

post_nodes = response.css("#archive .floated-thumb .post-thumb a")
for post_node in post_nodes:
    image_url = post_node.css("img::attr(src)").extract_first("")
    post_url = post_node.css("::attr(href)").extract_first("")
    yield Request(url=parse.urljoin(response.url, post_url), meta={"front_image_url":image_url}, callback=self.parse_detail)

#提取下一页并交给scrapy进行下载
next_url = response.css(".next.page-numbers::attr(href)").extract_first("")
if next_url:
    yield Request(url=parse.urljoin(response.url, next_url), callback=self.parse)
```

**scrapy的item类**

item只有Filed一个类型

使用item与pipeline保存数据
> * 在item中定义需要保存的内容，然后在pipeline处理item，爬虫流程就成了这样：
抓取 --> 按item规则收集需要数据 --> 使用pipeline处理（存储等）

如果我们获取了图片的地址想保存图片怎么办？
scrapy提供了图片自动下载机制

文件的打开和关闭：codecs
import codecs

`self.file = codes.open('article.json', 'w', encoding="utf-8") #w代表以写的方式打开`

scrapy.exporters可以协助pipeline将item导出为各种格式
例如导出json
from scrapy.exporters import JsonItemExporter


与mysql的整合

```
import MySQLdb
class MysqlPipeline(object):
#采用同步的机制写入mysql
    def __init__(self):
        self.conn = MySQLdb.connect('host', 'user', 'password', 'dbname', charset="utf8", use_unicode=True)
        self.cursor = self.conn.cursor() #执行数据库
    def process_item(self, item, spider):
        insert_sql = """
            insert into jobbole_article(title, url, create_date, fav_nums)
            VALUES (%s, %s, %s, %s)
        """
        self.cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))
  self.conn.commit()
execute执行时会执行完再向下进行，造成阻塞
可以种连接池来使数据库操作变成异步操作

可以将mysql的配置信息写在setting中
MYSQL_HOST = "127.0.0.1"
MYSQL_DBNAME = "article"
MYSQL_USER = "root"
MYSQL_PASSWORD = "1234"
然后在pipelines中通过@classmethod引入
通过from twisted.enterprise import adbapi来调用twisted来异步

from twisted.enterprise import adbapi
 
import codecs
import MySQLdb
import MySQLdb.cursors
class MysqlTwistedPipeline(object):
    def __init__(self, dbpool):
        self.dbpool = dbpool
    @classmethod
    def from_settings(cls, settings):
        dbparms = dict(
            host = settings["MYSQL_HOST"],
            db = settings["MYSQL_DBNAME"],
            user = settings["MYSQL_USER"],
            passwd = settings["MYSQL_PASSWORD"],
            charset = "utf8",
            cursorclass = MySQLdb.cursors.DictCursor,
            use_unicode = True,
        )
        dbpool = adbapi.ConnectionPool("MySQLdb", **dbparms)
        return cls(dbpool)
    def process_item(self, item, spidre):
        #使用twisted将mysql插入变成异步执行
        query = self.dbpool.runInteraction(self.do_insert, item)
        query.addErrback(self.handle_error)#处理异常
    def handle_error(self, failure):4
        #处理异步插入的异常
        print (failure)
    def do_insert(self, cursor, item):
        #执行具体的插入
        insert_sql = """
                    insert into jobbole_article(title, url, url_object_id, create_date, fav_nums)
                    VALUES (%s, %s, %s, %s, %s)
                """
        cursor.execute(insert_sql,(item["title"], item["url"], item["url_object_id"], item["create_date"], item["fav_nums"]))

使用ItemLoader爬取数据
from scrapy.loader import ItemLoader
#通过item loader加载item
item_loader = ItemLoader(item=JobbolenArticleItem(), response=response)
item_loader.add_css("title", ".entry-header h1::text")
item_loader.add_css("create_date", "p.entry-meta-hide-on-mobile::text")
item_loader.add_css("praise_nums", ".vote-post-up h10::text")
item_loader.add_css("comment_nums", "a[href='#article-comment'] span::text")
item_loader.add_css("fav_nums", ".bookmark-btn::text")
item_loader.add_css("tags", "p.entry-meta-hide-on-mobile a::text")
item_loader.add_css("content", "div.entry")
item_loader.add_value("url", response.url)
item_loader.add_value("url_object_id", get_md5(response.url))
item_loader.add_value("front_image_url", [front_image_url])

article_item = item_loader.load_item()

可以通过在 item中编写处理函数
import scrapy
import datetime
import re
from scrapy.loader.processors import MapCompose, TakeFirst, Join
from scrapy.loader import ItemLoader

class ArticlespiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    pass

def add_jobbole(value):
    return value+"-bobby"

def date_convert(value):
    try:
        create_date = datetime.datetime.strptime(value, "%Y/%m/%d").date()
    except Exception as e:
        create_date = datetime.datetime.now().date()
    return create_date

def get_nums(value):
    match_re = re.match(".*?(\d+).*", value)
    if match_re:
        nums = int(match_re.group(1))
    else:
        nums = 0
    return nums

def remove_commit_tags(value):
    #去掉tag中提取的评论
    if "评论" in value:
        return ""
    else:
        return value

def return_value(value):
    return value

class ArticleItemLoader(ItemLoader):
    #自定义itemLoader
    default_output_processor = TakeFirst()

class JobbolenArticleItem(scrapy.Item):
    title = scrapy.Field()
    create_date = scrapy.Field(
        input_processor = MapCompose(date_convert),
        #output_processor = TakeFirst() #只取第一个
    )
    url = scrapy.Field()
    url_object_id = scrapy.Field()
    front_image_url = scrapy.Field(
        output_processor = MapCompose(return_value)
    )
    front_image_path = scrapy.Field()
    praise_nums = scrapy.Field(
        input_processor = MapCompose(get_nums),
    )
    comment_nums = scrapy.Field(
        input_processor=MapCompose(get_nums),
    )
    fav_nums = scrapy.Field(
        input_processor=MapCompose(get_nums)
    )
    tags = scrapy.Field(
        input_processor=MapCompose(remove_commit_tags),
        output_processor=Join(",")
    )
    content = scrapy.Field()
```
