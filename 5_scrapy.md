## 1. scrapy
### 1.1 scrapy的创建和运行

1. 安装：`pip install scrapy`
2. 创建scrapy项目：终端输入 `scrapy startproject 项目名称`
3. 项目组成：
```
├──spiders 
|			├──__init__.py
|			├──自定义的爬虫文件.py：由我们自己创建，是实现爬虫核心功能的文件
├──__init__.py
├──items .py：定义数据结构的地方，是一个继承自scrapy.Item的类
├──middlewares.py：中间件，代理
├──pipelines.py：管道文件，里面只有一个类，用于处理下载数据的后续处理默认是300，优先级，值越小优先级越高(1-100)
├──settings.py：配置文件，比如: 是否遵守robots协议，user-Agent定义等
```

1. 创建爬虫文件：
   1. 跳转到spiders文件夹 `**cd 目录名字/目录名字/spiders**`
      1. 注意：项目名字不能使用数字开头，也不能有中文
   2. 创建爬虫文件 `**scrapy genspider 爬虫名字 网页的域名**` 
      1. eg：`**scrapy genspider baidu **[**http://www.baidu.com**](http://www.baidu.com)`
   3. 起始的爬虫文件结构：
```python
import scrapy


class BaiduSpider(scrapy.Spider):
    # 爬虫的名字，用于运行爬虫的时候使用的值
    name = 'baidu'
    # 允许访问的域名，注意这里要手动加 http:// 协议名
    allowed_domains = ['http://www.baidu.com'] 
    # 起始的url地址
    # start_urls 是在 allowed_domains 前添加一个 http://协议名，以及后面添加一个/
    start_urls = ['http://www.baidu.com/']

    # 执行了起始url之后，执行的方法，方法中的response就是返回的那个对象
    # 相当于 response = urllib.request.urlopen()
    #       response = request.get()
    def parse(self, response):
        pass
        
```

   4. 运行爬虫代码：`**scrapy crawl 爬虫的名字**`
      1. `**scrapy crawl baidu**`** **
   5. 修改协议：在 setting.py 中：`**ROBOTSTXT_OBEY = False**`** 或注释掉，**这样就不遵守robots协议了
2. 爬虫文件的基本组成：
   1. 继承scrapy.Spider类
      1. `name = 'baidu'`	：运行爬虫文件时使用的名字
      2. `allowed_domains`	：爬虫允许的域名，在爬取的时候，如果不是此域名之下的url，会被过滤掉
      3. `start_urls` ： 声明了爬虫的起始地址，可以写多个url，一般是一个
      4. `sparse(self，response)`：解析数据的回调函数
         1. `**response.text**`：响应的是字符串
         2. `**response.body**`：响应的是二进制文件
         3. `**response.xpath()**`：xpath方法的返回值类型是selector列表
         4. `**extract()**`：提取的是selectoer对象的是data
         5. `**extract_first()**`**	**：提取的是selectoer列表中的第一个数据
### 1.2 scrapy的架构组成

1. 引擎 ：自动运行，无需关注，会自动组织所有的请求对象，分发给下载器
2. 下载器：从引擎处获取到请求对象后，请求数据：
3. spiders：类定义了如何爬取某个(或某些)网站。包括了爬取的动作(例如:是否跟进链接)以及如何从网页的内容中提取结构化数据(爬取item)。 换句话说，Spider就是您定义爬取的动作及分析某个网页(或者是有些网页)的地方。
4. 调度器：有自己的调度规则，无需关注
5. 管道 (Item pipeline)	：最终处理数据的管道，会预留接口供我们处理数据
   1. 当item在spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对Item的处理每个item pipeline组件(有时称之为"Item Pipeline")是实现了简单方法的Python类。他们接收到tem并通过它执行一些行为，同时也决定此item是否继续通过pipeline，或是被丢弃而不再进行处理。

以下是item pipeline的一些典型应用：

1. 清理HTML数据
2. 验证爬取的数据(检查item包含某些字段)
3. 查重(并丢)
4. 将爬取结果保存到数据库中
### 1.3 scrapy的工作原理
> 和打麻将一个原理？

![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673761249991-2f572399-1d60-4682-86b8-cc2213fdb2d2.png#averageHue=%23f0eeed&clientId=u19e49ab2-311b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=409&id=u0868a54a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=409&originWidth=731&originalType=binary&ratio=1&rotation=0&showTitle=false&size=212556&status=done&style=none&taskId=u2d326639-94b6-400e-99cb-f5d833e44a4&title=&width=731)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673761177475-5b87d2dd-81f5-401d-8a92-57cd0eda6c17.png#averageHue=%23f9f2ec&clientId=u19e49ab2-311b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=617&id=u8ea63229&margin=%5Bobject%20Object%5D&name=image.png&originHeight=617&originWidth=871&originalType=binary&ratio=1&rotation=0&showTitle=false&size=238282&status=done&style=none&taskId=u39498c06-ea19-42d3-8de0-5890fc2362b&title=&width=871)
```python
import scrapy


class CarSpider(scrapy.Spider):
    name = 'car'
    allowed_domains = ['https://car.autohome.com.cn/price/brand-40.html']
    # 注意，如果请求的接口是html为结尾，那么不需要加/
    start_urls = ['https://car.autohome.com.cn/price/brand-40.html']

    def parse(self, response):
        name_list = response.xpath('//div[@class="main-title"]/a/text()')
        price_list = response.xpath('//span[@class="font-arial"]/text()')
        for i in range(len(name_list)):
            name = name_list[i].extract()
            price = price_list[i].extract()
            print(name, price)

```
## 2. scrapy shell

1. 什么是scrapy shell?

Scrapy终端，是一个交互终端，供您在未启动spider的情况下尝试及调试您的爬取代码。其本意是用来测试提取数据的代码，不过您可以将其作为正常的Python终端，在上面测试任何的python代码。
该终端是用来测试xpath或CSS表达式，查看他们的工作方式及从爬取的网页中提取的数据。在编写您的spider时，该终端提供了交互性测试您的表达式代码的功能，免去了每次修改后运行spider的麻烦。
一旦熟悉了scrapy终端后，您会发现其在开发和调试spider时发挥的巨大作用。
> **scrapy shell适合新手进行调试**

2. 安装ipython
   1. 安装：`pip install ipython` 
   2. 简介：如果您安装了 IPython，终端将使用 IPython (替代标准Python终端)。IPython相比更为强大，提供智能的自动补全，高亮输出，及其他特性。
3. 应用：**注意以下语句要在终端中输入，不要在ipython中输入**
   1. `scrapy shell www.baidu.com`
   2. `scrapy shell [http://www.baidu.com](http://www.baidu.com)`
   3. `scrapy shell "[http://www.baidu.com](http://www,baidu.com")"`
   4. `scrapy shell "www.baidu.com"`
4. 语法:
   1. response对象：
      1. `response.body`
      2. `response.text`
      3. `response.url`
      4. `response.status`

![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673763151319-448e8953-624b-4493-aeeb-5e2bd7955600.png#averageHue=%232f2e2d&clientId=u19e49ab2-311b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=648&id=u0ab8ca4f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=648&originWidth=1889&originalType=binary&ratio=1&rotation=0&showTitle=false&size=159529&status=done&style=none&taskId=uc0fa1b26-16b3-46b6-af23-e0f262a75df&title=&width=1889)

   2. response的解析：
      1. `response.xpath()` (常用)：使用xpath路径查询特定元素，返回一个selector列表对象
      2. `response.css()`：使用css_selector查询元素，返回一个selector列表对象
         1. 获取内容：`response.css('#su::text').extract_first()`** 不建议使用CSS语法**
         2. 获取属性：`response.css('#su::attr("value")').extract_first()`
      3. selector对象(通过xpath方法调用返回的是selector列表)
         1. `extract()`
            1. 提取selector对象
            2. 如果提取不到值会报错
            3. 使用xpath请求到的对象是一个selector对象，需要进一步使用extract()方法
         2. `extract_first()`
            1. 提取seletor列表中的第一个值
            2. 如果提取不到值 会返回一个空值
            3. 返回第一个解析到的值，如果列表为空，此种方法也不会报错，会返回一个空值
         3. `xpath()`
         4. `css()`：注意: 每一个selector对象可以再次的去使用xpath或者css方法

![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673763442944-2b7296a1-8556-4e35-a79d-38e4f7ca440c.png#averageHue=%232c2b2b&clientId=u19e49ab2-311b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=521&id=ucbe7d759&margin=%5Bobject%20Object%5D&name=image.png&originHeight=521&originWidth=1176&originalType=binary&ratio=1&rotation=0&showTitle=false&size=49196&status=done&style=none&taskId=u27f643a1-c80b-4503-a21c-5b2dc289c13&title=&width=1176)
## 3. yield

1. 带有 yield 的函数不再是一个普通函数，而是一个生成器 generator ，可用于迭代。
2. yield 是一个类似 return 的关键字，迭代一次遇到 yield 时就返回 yield 后面(右边)的值。重点是: **下一次迭代时，从上一次迭代遇到的yield后面的代码(下一行)开始执行。**(前端er会发现这个和es6处理异步的生成器很像)
3. 简要理解：**yield 就是 return 返回一个值，并且记住这个返回的位置，下次迭代就从这个位置后(下一行)开始。**

案例:

1. 当当网
   1. yield
   2. 管道封装
   3. 多条管道下载
   4. 多页数据下载
2. 电影天堂 
   1. 一个item包含多级页面的数据

当当网：
`ctrl+z`暂停
dang.py
```python
import scrapy
from scrapy_dangdang.items import ScrapyDangdangItem

class DangSpider(scrapy.Spider):
    name = 'dang'
    # 如果是多页下载，那么需要调整 allowed_domains 的范围，一般只写域名
    allowed_domains = ['category.dangdang.com']
    start_urls = ['http://category.dangdang.com/cp01.03.41.00.00.00.html']
    base_url = 'http://category.dangdang.com/pg'
    page = 1

    def parse(self, response):
        # pipelines 下载数据
        # items 定义数据结构
        # src = //ul[@id="component_59"]/li//img/@src
        # alt = //ul[@id="component_59"]/li//img/@alt
        # price = //ul[@id="component_59"]/li//p[@class="price"]/span[1]/text()
        # 所以的selector的对象，都可以再次调用xpath方法
        li_list = response.xpath('//ul[@id="component_59"]/li')
        for li in li_list:
            id = str(self.page) + '_' + str(li_list.index(li))
            src = li.xpath('.//img/@data-original').extract_first()
            # 注意第一张图片和其他图片标签的属性是不一样的，第一张的src是可以使用的，其他图片的地址是data-original
            if src:
                src = src
            else:
                src = li.xpath('.//img/@src').extract_first()
            name = li.xpath('.//img/@alt').extract_first()
            price = li.xpath('.//p[@class="price"]/span[1]/text()').extract_first()

            book = ScrapyDangdangItem(id=id, src=src, name=name, price=price)

            # 获取一个book，就将book交给pipelines
            yield book

        # 每一页的爬取的业务逻辑全部都是一样的，所以我们只需要执行的那个页的请求再次调用parse方法就可以了
        # http://category.dangdang.com/pg2-cp01.03.41.00.00.00.html
        if self.page < 10:
            self.page = self.page + 1
            url = self.base_url + str(self.page) + '-cp01.03.41.00.00.00.html'
            # 调用parse方法 scrapy的get请求
            # url时候请求地址，callback回调函数，此处相当于递归调用
            yield scrapy.Request(url=url, callback=self.parse)

```
items.py
```python
import scrapy


class ScrapyDangdangItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    # 通俗说就是你下载的数据有什么
    # 给书编号
    id = scrapy.Field()
    # 图片
    src = scrapy.Field()
    # 书名
    name = scrapy.Field()
    # 价格
    price = scrapy.Field()
    pass

```
pipelines.py
```python
import urllib.request


# 如果想使用管道，那么就必须在setting中开启管道
class ScrapyDangdangPipeline:
    # 在爬虫文件开始之前就执行的方法
    def open_spider(self, spider):
        self.fp = open('book.json', 'w', encoding='utf-8')

    # item就是yield后面的book对象
    def process_item(self, item, spider):
        # 1. w方法必须是str类型，不能使其他对象
        # 2. w会每一个对象都打开一次文件，覆盖之前的内容，然后关闭，写a(append)
        # 3. 以下这种模式不推荐，每传递一个对象，就打开一个文件，对文件的操作过于频繁
        # with open('book.json', 'a', encoding='utf-8')as fp:
        #     fp.write(str(item) + ',')

        self.fp.write(str(item) + ',')
        return item

    # 在爬虫文件执行完之后就执行的方法
    def close_spider(self, spider):
        self.fp.close()


# 多条管道同时开启
# 1. 定义管道类
# 2. 在settings中开启管道
class DangDangDownloadPipeline:
    def process_item(self, item, spider):

        url = 'http:' + item.get('src')
        filename = './books/' + item.get('id') + item.get('name') + '.jpg'
        urllib.request.urlretrieve(url=url, filename=filename)

        return item
```
settings.py
```python
...
ITEM_PIPELINES = {
   # 管道可以有很多个，管道有优先级，优先级的范围是1到1000，值越小优先级越高
   # 下载text数据的管道
   'scrapy_dangdang.pipelines.ScrapyDangdangPipeline': 300,
   # 下载图片的管道
   'scrapy_dangdang.pipelines.DangDangDownloadPipeline': 301,
}
...
```
电影天堂：跳转入对应链接，分步爬取
mv.py
```python
import scrapy

from scrapy_movie.items import ScrapyMovieItem


class MvSpider(scrapy.Spider):
    name = 'mv'
    allowed_domains = ['m.dytt8.net']
    start_urls = ['https://m.dytt8.net/html/gndy/china/index.html']

    def parse(self, response):
        # 要第一页的名字 和 第二页的图片
        id = 0
        a_list = response.xpath('//div[@class="co_content8"]//td[2]//a[2]')
        for a in a_list:
            # 获取第一页的名字和第二页要点击的链接
            id = id + 1
            name = a.xpath('./text()').extract_first()
            href = a.xpath('./@href').extract_first()
            # 第二页的地址
            url = 'https://m.dytt8.net/' + href
            # 对第二页的链接发起访问
            yield scrapy.Request(url=url, callback=self.parse_second, meta={'id': str(id), 'name': name})

    def parse_second(self, response):
        # 注意这里有坑，因为 span 识别不了
        # 注意，如果拿不到数据的情况下，一定要检查xpath语法是否正确
        src = response.xpath('//div[@id="Zoom"]//img/@src').extract_first()
        # 接收到请求的那个meta参数的值
        id = response.meta['id']
        name = response.meta['name']
        movie = ScrapyMovieItem(id=id, src=src, name=name)
        yield movie

```
items.py
```python
import scrapy


class ScrapyMovieItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    id = scrapy.Field()
    name = scrapy.Field()
    src = scrapy.Field()
    pass
```
pipelines.py
```python
import urllib.request

class ScrapyMoviePipeline:

    def open_spider(self, spider):
        self.fp = open('movie.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        self.fp.write(str(item) + ',')
        return item

    def close_spider(self, spider):
        self.fp.close()


class MovieImgDownloadPipeline:
    def process_item(self, item, spider):
        url = item.get('src')
        filename = './moviePics/' + item.get('id') + '_' + item.get('name') + '.jpg'
        urllib.request.urlretrieve(url=url, filename=filename)
        return item
```
settings.py
```python
...
ROBOTSTXT_OBEY = False
...
ITEM_PIPELINES = {
   'scrapy_movie.pipelines.ScrapyMoviePipeline': 300,
   'scrapy_movie.pipelines.MovieImgDownloadPipeline':301
}
...
```
## 4. Mysql

1. 下载([https://dev.mysql.com/downloads/windows/installer/5.7.html)](https://dev.mysql.com/downloads/windows/installer/5.7.html))
2. 安装([https://iingyan.baidu.com/album/d7130635f1c77d13fdf475df.html)](https://iingyan.baidu.com/album/d7130635f1c77d13fdf475df.html))
## 5. pymysql的使用步骤

1. 安装：`pip install pymysql`
2. 配置：`pymysql.connect(host, port, user, password, db, charset)`
3. 链接：`conn.cursor()`
4. 执行：`cursor.execute(sql)`
5. 提交：`conn.commit()`
6. 关闭：`cursor.close()`、`conn.close()`
## 6. CrawlSpider

1. 继承自scrapy.Spider
2. 独门秘笈
   1. Crawlspider可以定义规则，再解析htm内容的时候，可以根据链接规则提取出指定的链接，然后再向这些链接发送请求。
   2. 所以，如果有需要跟进链接的需求，意思就是爬取了网页之后，需要提取链接再次爬取，使用Crawlspider是非常合适的。
3. 提取链接：

链接提取器，在这里就可以写规则提取指定链接
```python
scrapy.linkextractors.LinkExtractor(
    allow = (), # 正则表达式提取符合正则的链接
    deny = (), # (不用)正则表达式 不提取符合正则的链接
    allow_domains = (), # (不用) 允许的域名
    deny_domains = (), # (不用)不允许的域名
    restrict_xpaths = (), # xpath，提取符合xpath规则的链接
    restrict css = () # 提取符合选择器规则的链接
)
```

4. 模拟使用
   1. 正则用法：`links1 = LinkExtractor(allow=r'地址_\d+\.html')`
   2. xpath用法：`links2 = LinkExtractor(restrict_xpaths=r'//div[@class="x"]')`
   3. css用法：`links3 = LinkExtractor(restrict_css='.x')`
5. 提取连接：`link.extract_links(response)`
6. 注意事项
   1.  callback只能写函数名字符串，`callback='parse_item'`
   2. 在基本的spider中，如果重新发送请求，那里的callback写的是`callback=self.parse_item `
   3.  `**follow=true**`** 是否跟进，就是按照提取连接规则进行提取**

运行原理：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673848810307-0f703c6e-dbe2-4df8-ae4d-af28e948047e.png#averageHue=%23f5e9e7&clientId=u2b579f1f-fc85-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=396&id=u6634fb62&margin=%5Bobject%20Object%5D&name=image.png&originHeight=396&originWidth=842&originalType=binary&ratio=1&rotation=0&showTitle=false&size=195581&status=done&style=none&taskId=u6ab02986-bae3-4725-8a94-7309e88a2f2&title=&width=842)
案例：读书网数据入库

1. 创建项目: `scrapy startproject scrapy_reading`
2. 跳转到 spiders 路径：`cd .\scrapy_reading\scrapy_reading\spiders`
3. 创建爬虫类：`scrapy genspider -t crawl read www.dushu.com`
4. items
5. spiders
6. settings
7. pipelines
   1. 数据保存到本地
   2. 数据保存到mysql数据库
## 7. 数据入库
案例：读书网数据入库
爬取的数据包括书名，书本图片链接、书本简介、书本价格、是否可购物
未解决问题：**无法爬取推广链接，在html结构上找不到直接跳转到京东的连接，个人认为可能是藏在js脚本中。**
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673870376570-69100dfb-2d62-4253-81db-b127c95fcbbd.png#averageHue=%23e8e7e5&clientId=u66962105-a707-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=899&id=u5de54dd5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=899&originWidth=1621&originalType=binary&ratio=1&rotation=0&showTitle=false&size=336721&status=done&style=none&taskId=u5d6f2961-7291-46da-969f-c4b3239929c&title=&width=1621)
settings.py配置参数：
```python
...
# Configure database
DB_HOST ='192.168.231.128'
DB_PORT = 3306
DB_USER ='root'
DB_PASSWORD = ...
DB_NAME = 'spider01'
DB_CHARSET ='utf8'

# Configure item pipelines
ITEM_PIPELINES = {
   'scrapy_reading.pipelines.ScrapyReadingPipeline': 300,
   'scrapy_reading.pipelines.MysqlPipeline': 301
}
...
```
pipelines.py：
```python
class ScrapyReadingPipeline:
    def open_spider(self, spider):
        self.fp = open('books.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        self.fp.write(str(item) + ',')
        return item

    def close_spider(self, spider):
        self.fp.close()


# 加载setting文件
from scrapy.utils.project import get_project_settings
import pymysql


class MysqlPipeline:

    def open_spider(self, spider):
        settings = get_project_settings()
        self.host = settings['DB_HOST']
        self.port = settings['DB_PORT']
        self.user = settings['DB_USER']
        self.password = settings['DB_PASSWORD']
        self.name = settings['DB_NAME']
        self.charset = settings['DB_CHARSET']
        self.connect()

    def connect(self):
        self.conn = pymysql.connect(host=self.host, port=self.port, user=self.user, password=self.password, db=self.name, charset=self.charset)
        self.cursor =self.conn.cursor()

    def process_item(self, item, spider):
        sql = 'insert into book(`book_name`, `src`, `author`, `is_buy`, `price`, `desc`) values("{}","{}","{}","{}","{}","{}")'.format(
            item.get("name"), item.get("src"), item.get("author"), item.get("is_buy"), item.get("price"), item.get('desc')
        )
        self.cursor.execute(sql)
        self.conn.commit()
        return item

    def close_spider(self, spider):
        self.cursor.close()
        self.conn.close()


    
```
read.py：
```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule

from scrapy_reading.items import ScrapyReadingItem


class ReadSpider(CrawlSpider):
    name = 'read'
    allowed_domains = ['www.dushu.com']
    # 注意加上_1，否则爬取不到第一页的数据
    start_urls = ['https://www.dushu.com/book/1188_1.html']

    rules = (
        # 这个会过页爬取
        Rule(LinkExtractor(allow=r'/book/1188_\d+\.html'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        books_list = response.xpath('//div[@class="bookslist"]//li')
        for b in books_list:

            src = b.xpath('.//img/@data-original').extract_first()
            name = b.xpath('.//img/@alt').extract_first()
            author = b.xpath('.//p[1]/text()').extract_first().split(' ')[0]
            is_buy = b.xpath('.//p[3]/span/text()').extract_first()

            href = b.xpath('.//a/@href').extract_first()
            url = 'https://www.dushu.com' + href

            yield scrapy.Request(url=url, callback=self.second_parse_item,
                                 meta={'src': src, 'name': name, 'author': author, 'is_buy': is_buy})

    def second_parse_item(self, response):
        name = response.meta['name']
        src = response.meta['src']
        author = response.meta['author']
        is_buy = response.meta['is_buy']

        price = response.xpath('//p[@class="price"]/span/text()').extract_first()
        desc = response.xpath('//div[@class="book-summary"][1]//div[1]//div[1]/text()').extract_first().replace('\u3000\u3000','').replace(' ','')

        book = ScrapyReadingItem(name=name, src=src, author=author, is_buy=is_buy, price=price, desc=desc)
        yield book
```
item.py
```python
import scrapy


class ScrapyReadingItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    name = scrapy.Field()
    src = scrapy.Field()
    author = scrapy.Field()
    is_buy = scrapy.Field()
    price = scrapy.Field()
    desc = scrapy.Field()
    pass

```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673870499096-6cab5312-87a6-4f88-9f96-085eb361f465.png#averageHue=%23f1eeeb&clientId=u66962105-a707-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1031&id=uf2a3c2a8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1031&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=358733&status=done&style=none&taskId=u62857591-cc97-4d41-a327-28d5843610d&title=&width=1920)
## 8. 日志信息和日志等级

1. 日志级别:
   1. **CRITICAL**：严重错误
   2. **ERROR**：一般错误
   3. **WARNING**：警告
   4. **INFO**：一般信息
   5. **DEBUG**：调试信息
> **默认的日志等级是DEBUG，只要出现了DEBUG或者DEBUG，以上等级的日志那么这些日志将会打印**

2. settings.py文件设置
   1. 默认的级别为DEBUG，会显示上面所有的信息
   2. 在配置文件中 settings.py
   3. **LOG_FILE**：将屏幕显示的信息全部记录到文件中，屏幕不再显示，注意文件后缀一定是.log
   4. **LOG_LEVEL** : 设置日志显示的等级，就是显示哪些，不显示哪些
```python
# LOG_LEVEL='WARNING'
LOG_FILE='log_demo.log'
```
## 9. scrapy的post请求

1. 重写 start_requests 方法：`def start requests(self)`
2. start requests 的返回值：`scrapy.FormRequest(url=url, headers=headers, callback=self, parse_item, formdata=data)`
   1. url： 要发送的post地址
   2. headers：可以定制头信息
   3. callback：回调函数
   4. formdata：post所携带的数据，这是一个字典

案例，还是百度翻译
post.py
```python
import json
import scrapy


class PostSpider(scrapy.Spider):
    name = 'post'
    allowed_domains = ['fanyi.baidu.com']
    # post请求如果没有参数，那么这个请求将没有任何意义
    # 所以start_urls也没有用了
    # parse方法也没有用了
    # start_urls = ['https://fanyi.baidu.com/sug/']

    # def parse(self, response):
    #     pass

    def start_requests(self):
        url = 'https://fanyi.baidu.com/sug/'
        data = {
            'kw': 'crawl'
        }
        yield scrapy.FormRequest(url=url, formdata=data, callback=self.parse_second)

    def parse_second(self, response):
        content = response.text
        obj = json.loads(content)
        print(obj)
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673860712452-f6bb7991-00f3-41cb-9f3c-2ddf195bd1e1.png#averageHue=%23302f2e&clientId=u9511493e-0b19-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=497&id=u520ae7b7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=497&originWidth=1900&originalType=binary&ratio=1&rotation=0&showTitle=false&size=139944&status=done&style=none&taskId=u9577815f-baae-4188-a835-17decc1f2d6&title=&width=1900)
## 10. 代理

1. 重写start_request方法：
```python
def start_requests(self):
    for url in self.start_urls:
        yield scrapy.Request(url,callback=self.parse, meta={'proxy':'https://113.68.202.10:9999'})
```

2. 到 settings.py 中，打开一个选项：
```python
DOWNLOADER_MIDDLEWARES = {
   'postproject .middlewares.Proxy' : 543,
}
```

3. 到middlewares.py中写代码
```python
def process_request(self, request, spider):
        request.meta['proxy'] = 'https://113.68.202.10:9999'
        return None
```
