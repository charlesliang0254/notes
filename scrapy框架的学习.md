[TOC]

## 一、scrapy框架环境配置

1、安装并配置anaconda环境
2、配置anaconda环境的镜像：
```
conda config --add channels 镜像地址
conda config --set show_channel_urls yes
```
执行命令之后会在c:\users\username\下生成一个.condaarc文件，可以使用编辑器直接修改
3、安装scrapy

```
conda install scrapy
```
## 二、创建一个项目
1、创建一个scrapy项目
```
scrapy startproject helloworld
```
2、项目结构如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191111112504636.png)
scrapy.cfg：部署配置文件
helloworld：项目的python模块，从此处导入你的代码
items.py：数据项定义
middlewares.py：定义下载器中间件和蜘蛛中间件的位置
piplines.py：定义Item Pipline，实现数据的清洗、储存、验证
settings.py：全局配置
spiders：用于放置爬虫的目录

3、创建一个spider
打开一个终端，将当前目录切换到spiders下，然后执行命令
```
scrapy genspider demo maoyan.com
```
scrapy会创建一个py文件，内容如下
```python
# -*- coding: utf-8 -*-
import scrapy


class DemoSpider(scrapy.Spider):
    name = 'demo'  # 项目的名字
    allowed_domains = ['maoyan.com']  # 允许爬取的域名
    start_urls = ['http://maoyan.com/']  # 爬取的网站，可以定义多个初始的url

	# 请求初始url之后执行的方法，主要用于对网页的解析与获取想要的数据
	# response 请求网页后返回的内容
    def parse(self, response):
        pass

```

4、定义items
item是保存爬取数据的容器，定义方式和字典类似
```python
import scrapy


class HelloworldItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    index = scrapy.Field()
    title = scrapy.Field()
    star = scrapy.Field()
    release_time = scrapy.Field()
    score = scrapy.Field()

```

5、实现parse方法
```python
# -*- coding: utf-8 -*-
import scrapy
from helloworld.items import HelloworldItem


class DemoSpider(scrapy.Spider):
    name = 'demo'
    allowed_domains = ['maoyan.com']
    start_urls = ['http://maoyan.com/']

    def parse(self, response):
        dl = response.css(':board-wrapper dd')
        for dd in dl:
            item = HelloworldItem()
            item.index = dd.css('.board-index::text').extract_first()
            item.title = dd.css('.name a::text').extract_first()
            item.star = dd.css('.star::text').extract_first()
            item.release_time = dd.css('.releasetime::text').extract_first()
            item.score = dd.css('integer::text').extract_first()+dd.css('.fraction::text').extract_first()
            yield item

```

6、运行
将目录切换到scrapy.cfg所在目录下，并执行以下命令
```
scrapy crawl demo
```
## 三、入门教程

### 创建Spider

在spiders路径下创建一个py文件qutoes_spider.py，定义一个Spider如下
```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"  # 唯一标识爬虫

    # 返回爬虫开始爬取的url
    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)  # yield关键字与python的生成器有关，生成器中的元素只能被遍历一次

    # 处理每个请求得到的响应，通常用于处理响应、将爬取的数据抽取为字典或json、检索并添加新的请求
    def parse(self, response):
        page = response.url.split('/')[-2]
        filename = 'quotes-%s.html' % page
        with open(filename, 'wb') as f:
            f.write(response.body)  # 将响应存储为文件
        self.log('Saved file %s' % filename)

```
#### 运行过程：

Scrapy逐个发出request请求，一旦收到响应就立即创建一个Response对象，并执行与对应请求绑定的函数，将Response对象作为参数传入该函数（在本例中这个函数是parse）
对start_requests方法的简化：可以不定义这个方法，而是直接定义一个list类型的类属性

```python
class QuotesSpider(scrapy.Spider):
	name = "quotes"
	start_urls = [
		'http://quotes.toscrape.com/page/1/',
        'http://quotes.toscrape.com/page/2/',
	]
	...
```
### 提取数据：

提取数据的最好方法是使用scrapy shell

```powershell
scrapy shell "http://quotes.toscrape.com/page/1/"
```
在shell中可以利用css和response对象选择各个节点。
```powershell
response.css('title')
```

 返回值是一个类似list的对象SelectorList，其中每一个元素的类型是Selector，封装着xml/html元素并允许继续进行细粒度的检索。

得到title标签的文本值

```powershell
response.css('title::text').getAll()
# ::text表示只选择title标签的文本部分，如果不加，返回的还包括前后标签
# getAll()返回的结果可能是一个list，因为一个选择器有可能返回多个结果

# 如只需要得到一个结果，可以使用下面的语句
# 如果选择器没有选择任何元素，get函数不会抛出IndexError而是返回None
response.css('title::text').get()
response.css('title::text')[0].get()

# 除了以上方法还可以使用re()函数提取数据
response.css('title::text').re(r'Quotes.*')  # ['Quotes to Scrape']
response.css('title::text').re(r'Q\w+')  # ['Quotes']
response.css('title::text').re(r'(\w+) to (\w+)')  # ['Quotes','Scrape']
```

使用`view(response)`从浏览器打开响应页面，Selector Gadget是一个快速找到选择器的工具。

### XPath

Scrapy支持XPath表达式

```python
response.xpath('//title')
# [<Selector xpath='//title' data='<title>Quotes to Scrape</title>'>]
response.xpath('//title/text()').get()
# 'Quotes to Scrape'
```

XPath表达式是Scrapy选择器的底层实现机制，CSS选择器要先被转换为XPath才能够进行解析，官方推荐使用XPath。

### 提取quotes与author

```python
quote = response.css('span.text::text').get()
author = response.css('small.author::text').get()
tags = quote.css("div.tags a.tag::text").getall()
```

### 在爬虫中提取数据

```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
        'http://quotes.toscrape.com/page/2/'
    ]
    
    def parse(self, response):
        for quote in response.css('div.quote'):
            yield{
                'text':quote.css('span.text::text').get(),
                'author':quote.css('small.author::text').get(),
                'tags':quote.css('div.tags a.tag::text').getall()
            }
```

### 存储爬取的数据

1、使用命令行

```powershell
scrapy crawl tutorial -o quotes.json
```

2、使用pipline

### 跟随链接

获得链接：

```python
response.css('li.next a').get()
# <a href="/page/2/">Next <span aria-hidden="true">→</span></a>
response.css('li.next a::attr(href)').get()
# '/page/2/'
response.css('li.next a').attrid['href'].get()
# '/page/2/'
```

让我们的spider递归地跟随链接：

```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            next_page = response.urljoin(next_page)  # 使用urljoin方法得到一个完整的url地址
            yield scrapy.Request(next_page, callback=self.parse)
```

### 创建一个请求对象的捷径

```python
response.follow(next_page,callback=self.parse)
# 支持相对路径
# 返回一个Request对象

for a in response.css('li.next a'):
	response.follow(a,callback=self.parse)
	# follow方法能够自动调用a标签结点中的href属性

```

### 更多的示例和模式

```python
import scrapy


class AuthorSpider(scrapy.Spider):
    name = 'author'

    start_urls = ['http://quotes.toscrape.com/']

    def parse(self, response):
        # follow links to author pages
        for href in response.css('.author + a::attr(href)'):
            yield response.follow(href, self.parse_author)

        # follow pagination links
        for href in response.css('li.next a::attr(href)'):
            yield response.follow(href, self.parse)

    def parse_author(self, response):
        def extract_with_css(query):
            return response.css(query).get(default='').strip()

        yield {
            'name': extract_with_css('h3.author-title::text'),
            'birthdate': extract_with_css('.author-born-date::text'),
            'bio': extract_with_css('.author-description::text'),
        }
```

- 在这个例子中，我们首先对主页面进行解析，获得作者主页的链接，然后逐个地访问每一个作者的页面。
- Scrapy中的过滤器能够过滤掉那些被重复提交的url，避免针对多次重复访问同一页面，如果要自定义，可以配置DUPEFILTER_CLASS项

### 使用spider参数

`scrapy crawl quotes -o quotes-humor.json -a tag=humor`

通过-a可以向我们的spider提供参数，这些参数被传递给`__init__`方法，并默认成为spider的属性

```python
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        url = 'http://quotes.toscrape.com/'
        tag = getattr(self, 'tag', None)
        if tag is not None:
            url = url + 'tag/' + tag
        yield scrapy.Request(url, self.parse)

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            yield response.follow(next_page, self.parse)
```

## 四、命令行工具

### 配置项目

Scrapy在标准的位置寻找ini风格的配置文件scrapy.cfg

- `/etc/scrapy.cfg`或者`c:\scrapy\scrapy.cfg`（系统范围）
- `~/.config/scrapy`或者`~/.scrapy.cfg`（用户范围）
- scrapy项目中的scrapy.cfg（项目范围）

注意优先级：项目范围>用户范围>系统范围

Scrapy可以识别一些环境变量，通过这些环境变量可以配置Scrapy，例如：

- SCRAPY_SETTINGS_MODULE
- SCRAPY_PROJECT
- SCRPAY_PYTHON_SHELL

### Scrapy项目的默认结构

```yml
scrapy.cfg
myproject/
    __init__.py
    items.py
    middlewares.py
    pipelines.py
    settings.py
    spiders/
        __init__.py
        spider1.py
        spider2.py
        ...
```

注意，scrapy.cfg所在的目录是项目的根目录，scrapy.cfg中包含了定义项目配置项的python模块文件的名称

### 在项目之间分享根目录

同一个scrapy.cfg文件中可以定义多个settings.py文件

```python
[settings]
default = myproject1.settings
project1 = myproject1.settings
project2 = myproject2.settings
```

默认情况下，Scrapy将使用default配置项，使用SCRAPY_PROJECT环境变量可以配置要选择的项目

```powershell
$ scrapy settings --get BOT_NAME
Project 1 Bot
$ export SCRAPY_PROJECT=project2
$ scrapy settings --get BOT_NAME
Project 2 Bo
```

### 使用scrapy工具

不带任何参数的情况下运行`scrapy`命令会显示一些帮助信息和可用的命令。

如果你正处在一个scrapy项目的内部，第一行会显示当前处于激活状态的项目

#### 创建项目

`scrapy startproject myproject [project_dir]`

这个命令将会在project_dir下创建一个Scrapy项目，如果project_dir没有指定，那么默认为myproject

接下来，使用cd命令进入project_dir，就可以准备好使用scrapy命令去管理和控制项目了

#### 控制项目

创建一个新的爬虫spider：

`scrapy genspider mydomain mydomain.com`

注意一些命令必须要在scrapy项目中才能运行，例如crawl；还有一些命令在scrapy项目内部运行会有不同的效果，例如fetch命令，如果被取得的url与一些指定的spider相关联，那么fetch命令会使用spider重写的行为

#### 可用的工具命令

查看每个命令的用法：

`scrapy <command> -h`

查看所有可用的命令：

`scrapy -h`

两种不同的命令：1、只在scrapy项目内部运行的命令；2、可以无需一个激活的scrapy项目就能运行的命令，称之为全局命令（但可能在项目内部和外部有不同的效果）

全局命令包括：

- startproject
- genspider
- settings
- runspider
- shell
- fetch
- view
- version

项目专用命令包括：

- crawl
- check
- list
- edit
- parse
- bench

#### startproject

语法：`scrapy startproject <project_name> [project_dir]`

是否需要项目：否

创建一个新的scrapy项目命名为project_name，在project_dir目录之下。如果project_dir没有指定，则默认等于project_name

示例：`scrapy startproject myproject`

#### genspider

语法：`scrapy genspider [-t template] <name> <domain>`

是否需要项目：否

创建一个新的爬虫在当前目录下或者在当前项目的spiders目录下，name用于唯一表示爬虫的名称，domain用于产生allowed_domains和start_urls属性

示例：

```powershell
$ scrapy genspider -l
Available templates:
  basic
  crawl
  csvfeed
  xmlfeed

$ scrapy genspider example example.com
# 使用basic模板生成爬虫example

$ scrapy genspider -t crawl scrapyorg scrapy.org
# 使用crawl模板生成爬虫scrapyorg
```

当然你也可以直接创建并编写爬虫的源代码，这样也可以创建爬虫

#### crawl

语法：`scrapy crawl <spider>`

是否需要项目：是

使用爬虫开始爬取数据

示例：`scrapy crawl myspider`

#### check

语法：`scrapy check [-l] <spider>`

是否需要项目：是

执行协议检查

示例：

```powershell
$ scrapy check -l
first_spider
  * parse
  * parse_item
second_spider
  * parse
  * parse_item

$ scrapy check
[FAILED] first_spider:parse_item
>>> 'RetailPricex' field is missing

[FAILED] first_spider:parse
>>> Returned 92 requests, expected 0..4
```

#### list

语法：`scrapy list`

是否需要项目：是

列举当前项目中所有可用的spider

#### edit

语法：`scrapy edit <spider>`

是否需要项目：是

使用定义在EDITOR环境变量中或者EDITOR设置中的编辑器编辑指定的爬虫spider。如果开发人员没有使用任何工具或者IDE就可以使用这个命令简化操作

#### fetch

语法：`scrapy fetch <url>`

是否需要项目：否

使用scrapy下载器下载给定url并将内容写入标准输出。如果爬虫拥有一个USER_AGENT属性，覆盖了用户代理属性，那么它将使用那一个配置。所以这个命令可以查看你的spider将如何获取一个特定的页面。如果这个命令用在项目之外，没有任何特定的爬虫的行为被应用，那么它将使用默认的Scrapy下载器配置。

支持的选项：

- `--spider=SPIDER`：绕开爬虫自动检测并且强制特定spider的使用
- `--headers`：打印HTTP响应的信息头而不是响应的主体
- `--no-redirect`：不要跟随HTTP 3xx重定向请求

#### view

语法：`scrapy view <url>`

是否需要项目：否

在浏览器打开给定的url，当你的spider“看”到它的时候。

支持的选项：

- --spider=SPIDER`：绕开爬虫自动检测并且强制特定spider的使用
- `--no-redirect`：不要跟随HTTP 3xx重定向请求

#### shell

语法：`scrapy shell [url]`

是否需要项目：否

打开scrapy shell，可以指定url也可以不指定。同时支持UNIX风格的本地文件路径，包括`./`和`../`以及绝对路径。

支持的选项：

- `--spider=SPIDER`：绕开爬虫自动检测并且强制特定spider的使用
- `-c code`：解析shell中的代码，打印结果并退出
- `--no-redirect`：不要跟随HTTP 3xx重定向请求

#### parse

语法：`scrapy parse <url> [options]`

是否需要项目：是

取得给定的url并使用处理它的爬虫分析它，使用parse方法或者--callback指定的方法分析

支持的选项：

- `--spider=SPIDER`：绕过爬虫自动检测并强制使用指定的spider
- `--a NAME=VALUE`：给spider设置参数
- `--callback`或者`-c`：spider的方法，用作解析响应的会回调函数
- `--meta`或者`-m`：向回调函数传递附加的meta数据，参数必须是一个有效的json字符串
- `--cbkwargs`：向回调函数传递附加关键字参数，必须是一个有效的json字符串
- `--piplines`：通过piplines处理items
- `--rules`或者`-r`：使用CrawlSpider规则发现回调函数并处理响应
- `--noitems`：不显示爬取的items
- `--nolinks`：不显示提取的链接
- `--nocolour`：避免使用pygments指定输出的颜色
- `--depth`或者`-d`：跟随链接的深度级别
- `--verbose`或者`-v`：显示每一个深度层的信息

#### settings

语法：`scrapy settings [options]`

是否需要项目：否

获得Scrapy设置的值，如果是在项目内部获得的就是项目设置，如果在项目外就是全局设置

示例

```powershell
$ scrapy settings --get BOT_NAME
scrapybot
$ scrapy settings --get DOWNLOAD_DELAY
0
```

#### runspider

语法：`scrapy runspider <spider_file.py>

是否需要项目：否

在不创建一个项目的情况下运行一个spider

示例：

```powershell
$ scrapy runspider myspider.py
[ ... spider starts crawling ... ]
```

#### version

语法：`scrapy version [-v]

是否需要项目：否

打印Scrapy的版本，如果使用-v则也会打印python、twisted以及平台的信息

#### bench

语法：`scrapy bench`

是否需要项目：运行一个快捷的benchmark测试

### 自定义项目命令

使用`COMMAND_MODULE`设置，使用时要谨慎不再展开介绍

## 五、爬虫Spider

Spiders：定义如何爬取一个网站的类，包括怎样爬取数据和怎样从页面中提取有组织的数据。

爬虫执行周期如下：

1、通过生成初始Request对象爬取第一个url开始，针对每一个请求指定一个在接收到响应后执行的回调函数。第一个要执行的请求通过调用start_urls方法来获得

2、在回调函数中，解析响应并且返回提取的数据、Item对象、Request对象或者一个涵盖以上内容的可迭代容器。其中Request对象包含一个回调函数，在对应的响应被下载完成后这些指定的回调函数将被执行

3、在回调函数中可以解析页面内容，可以使用Selectors、BeautifulSoup、lxml或者其他任何你想用的机制，用解析得到的数据生成Item对象

4、最后items被爬虫返回，一般会通过Item Pipline持久化存储到数据库中或者使用Feed Export写入文件。

### scrapy.Spider

这是最简单的爬虫，任何其他类型的爬虫必须继承这个类，它没有提供任何多余的功能，只是提供了一个默认的start_requests()方法的实现，即从spider类属性start_urls中发送请求并且调用类方法parse处理每一个响应。

- `name`：唯一标识spider的名称

- `allowed_domains`：允许爬取的域名列表，如果OffsiteMiddleware启用则不允许跟随超出该列表的url

- `start_urls`：开始爬取的url列表

- `custom_settings`：自定义设置，字典

- `crawler`：这个属性被`from_crawler()`方法设定，链接到与Spider绑定的Crawler对象

- `settings`：运行爬虫的配置信息

- `logger`：用Spider的name属性值创建的python语言Logger

- `from_crawler(crawler, *args, **kwargs)`：用于创建Spider的方法，一般情况下不需要直接重写，因为默认实现起到构造方法的一个代理的作用。

- `start_requests()`：返回一个初始Request对象的可迭代容器用于爬取数据，只调用一次，可以安全地实现这个方法作为一个初始化器。

  你想开始爬取一个域名的请求对象，可以重写这个方法。例如，你想使用post请求登录作为爬取的开始，可以这样做：

  ```python
  class MySpider(scrapy.Spider):
      name = 'myspider'
  
      def start_requests(self):
          return [scrapy.FormRequest("http://www.example.com/login",
                                     formdata={'user': 'john', 'pass': 'secret'},
                                     callback=self.logged_in)]
  
      def logged_in(self, response):
          # here you would extract links to follow and return Requests for
          # each of them, with another callback
          pass
  ```

- `parse(response)`：默认的回调函数，用于解析响应和返回提取的数据和跟随的请求，必须返回Request对象、字典、Item对象或者它们的可迭代容器，自定义的回调函数同样如此。

- `log(message [,level,component])`：日志

- `closed(reason)`：在Spider关闭的时候调用