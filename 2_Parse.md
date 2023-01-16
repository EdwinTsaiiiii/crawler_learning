## xpath
### xpath使用
> 注意: 提前安装xpath插件
> 下载地址：[https://chrome.zzzmh.cn/info?token=hgimnogjllphhhkhlmebbmlgjoejdpjl](https://chrome.zzzmh.cn/info?token=hgimnogjllphhhkhlmebbmlgjoejdpjl)

1. 安装lxml库 `pip install lxml -i [https://pypi.douban.com/simple](https://pypi.douban.com/simple2.)`
2. 导入lxml.etree `from lxml import etree`
3. `etree.parse()`解析本地文件`html_tree = etree.parse('xx.html')`
4. `etree.HTML()`服务器响应文件`html_tree = etree.HTML(response.read().decode("utf-8"))`
5. `html_tree.xpath(xpath路径)`
### xpath基本语法

1. 路径查询

`//`: 查找所有子孙节点，不考虑层级关系
`/`: 找直接子节点

2. 谓词查询

`//div[@id]`
`//div[@id="maincontent"]`

3. 属性查询

`//@class`

4. 楼糊查询 

`//div[contains(@id,"he")]`
`//div[starts-with(@id,"he")]`

5. 内容查询 

`//div/h1/text()`

6. 逻辑运算 

`//div[@id="head" and @class="s_down"]` 
`//title | //price`
```python
from lxml import etree

# xpath解析
# 1.xpath解析本地文件：etree.parse()
tree = etree.parse('1_解析_xpath.html')
print(tree) # <lxml.etree._ElementTree object at 0x000002C72B6087C0>

# 2.xpath服务器响应数据 response.read().decode('utf-8') ：etree.HTML()
# li_list = tree.xpath('//body/ul/li')
li_list = tree.xpath('//body//li')
print(li_list) # [<Element li at 0x2c72b769400>, <Element li at 0x2c72b769380>, <Element li at 0x2c72b7693c0>, <Element li at 0x2c72b769440>]

# 查找所有有id属性的li标签
li_list_id = tree.xpath('//ul/li[@id]/text()')
print(li_list_id) # ['北京', '上海', '深圳', '武汉']

li_list_bj = tree.xpath('//ul/li[@id="beijing"]/text()')
print(li_list_bj) # ['北京']

# 查找到id为li的class属性值
li_class = tree.xpath('//ul/li[@id="beijing"]/@class')
print(li_class) # ['capital']

# 查询id中包含h的li标签
li_list_h = tree.xpath('//ul/li[contains(@id,"h")]/text()')
print(li_list_h) # ['上海', '深圳', '武汉']

# 查询id中以sh开头的li标签
li_list_sh = tree.xpath('//ul/li[starts-with(@id,"sh")]/text()')
print(li_list_sh) # ['上海', '深圳']

# 查询id为beijing和class为capital的标签
li_list_logic = tree.xpath('//ul/li[@id="beijing" and @class="capital"]/text()')
print(li_list_logic) # ['北京']

li_list_or = tree.xpath('//ul/li[@id="beijing"]/text() | //ul/li[@class="modu"]/text()')
print(li_list_or) # ['北京', '上海']
```
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8"/>
    <title>Title</title>
  </head>
  <body>
    <ul>
      <li id="beijing" class="capital">北京</li>
      <li id="shanghai" class="modu">上海</li>
      <li id="shenzhen">深圳</li>
      <li id="wuhan">武汉</li>
    </ul>
  </body>
</html>
```
案例：获取百度网站的百度一下
这回需要用到xpath插件，在百度页面上`ctrl+shift+x`:
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673582089377-7461ef1f-e2a6-41fb-8e5d-19db2c2c3538.png#averageHue=%23e8e6e4&clientId=u39ad9dc2-f794-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=938&id=u8b7ab513&margin=%5Bobject%20Object%5D&name=image.png&originHeight=938&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=488799&status=done&style=none&taskId=u79a09866-7702-429c-b50b-f696d51a29c&title=&width=1920)
```python
import urllib.request

# 获取网页源码
url = 'https://www.baidu.com/'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}
request = urllib.request.Request(url=url, headers=headers)
response = urllib.request.urlopen(request)
content = response.read().decode('utf-8')
# print(content)

# 解析网页源码，获取想要的数据
from lxml import etree
html_tree = etree.HTML(content)
result = html_tree.xpath('//input[@id="su"]/@value')[0]
print(result) # 百度一下
```
案例：xpath采集图片
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673583540470-14b36278-cd66-45ed-bb8f-c3cc6bb99432.png#averageHue=%23aa8f54&clientId=ue5644129-4849-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=932&id=udd534cc2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=932&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=960618&status=done&style=none&taskId=uf30ba984-c447-43f5-b186-6f47ffdd198&title=&width=1920)
注意图片网站一般底层都有懒加载，所以我们还需要观察未加载的图片放在哪一个属性中(data-original)：
未加载时，如果一上来就抓src，抓到的都是loading的图片：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673584088564-4068f89b-2df2-43f2-8d3b-00a13d3e6356.png#averageHue=%23ebf0d6&clientId=ue5644129-4849-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=114&id=u839dbdf5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=114&originWidth=980&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22912&status=done&style=none&taskId=u61c6e926-4ea7-4bef-bd54-d664bdd6a90&title=&width=980)
加载好了：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673584143314-450fee25-6592-429d-b40e-79ded672b852.png#averageHue=%23fef1ef&clientId=ue5644129-4849-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=109&id=u40377e6e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=109&originWidth=897&originalType=binary&ratio=1&rotation=0&showTitle=false&size=21170&status=done&style=none&taskId=ub6110cd6-4447-475e-ae14-d1c1dc99257&title=&width=897)
```python
import urllib.request
from lxml import etree

def create_request(page):
    if(page == 1):
        url = 'https://sc.chinaz.com/tupian/fengjing.html'
    else:
        url = 'https://sc.chinaz.com/tupian/fengjing_' + str(page) + '.html'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
    }
    request = urllib.request.Request(url=url, headers=headers)
    return request


def get_content(request):
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf-8')
    return content


def down_load(content):
    # xpath解析数据
    tree = etree.HTML(content)
    name_list = tree.xpath('//div[@class="container"][2]//div/img/@alt')
    # 这样获取不到图片，一般涉及图片的网站都会进行懒加载。
    # src_list = tree.xpath('//div[@class="container"][2]//div/img/@src')
    src_list = tree.xpath('//div[@class="container"][2]//div/img/@data-original')
    for i in range(len(name_list)):
        name = name_list[i]
        src = src_list[i]
        # 记得拼接上协议名，去掉_s即是高清图片
        url = ('https:' + src).replace('_s', '')
        # 下载图片
        urllib.request.urlretrieve(url=url, filename='./scene_img/' + name + '.jpg')


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))
    for page in range(start_page, end_page + 1):
        request = create_request(page)
        content = get_content(request)
        down_load(content)
```
爽歪歪：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673587236779-3e84ce10-2a0c-4591-ab8f-8ae403734e3b.png#averageHue=%23838b67&clientId=ue5644129-4849-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=729&id=ub75ebf16&margin=%5Bobject%20Object%5D&name=image.png&originHeight=729&originWidth=1125&originalType=binary&ratio=1&rotation=0&showTitle=false&size=167952&status=done&style=none&taskId=u34053efe-8edc-443f-a2cf-af847c8b71e&title=&width=1125)
## JsonPath
jsonpath的安装及使用方式：
pip安装：`pip install jsonpath`
jsonpath的使用：
`obj = json.load(open('json文件', 'r', encoding="utf-8"))`
`ret = jsonpath.jsonpath(obj, "jsonpath语法")`
教程：[https://blog.csdn.net/luxideyao/article/details/77802389](https://blog.csdn.net/luxideyao/article/details/77802389)
```json
{ "store": {
    "book": [
      { "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      { "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99
      },
      { "category": "fiction",
        "author": "Herman Melville",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99
      },
      { "category": "fiction",
        "author": "J. R. R. Tolkien",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95,
      "author": "Moly"
    }
  }
}
```
| **XPath** | **JSONPath** | **结果** |
| --- | --- | --- |
| /store/book/author | $.store.book[*].author | 书点所有书的作者 |
| //author | $..author | 所有的作者 |
| /store/* | $.store.* | store的所有元素。所有的bookst和bicycle |
| /store//price | $.store..price | store里面所有东西的price |
| //book[3] | $..book[2] | 第三个书 |
| //book[last()] | $..book[(@.length-1)] | 最后一本书 |
| //book[position()<3] | $..book[0,1]
$..book[:2] | 前面的两本书。 |
| //book[isbn] | $..book[?(@.isbn)] |  过滤出所有的包含isbn的书。 |
| //book[price<10] | $..book[?(@.price<10)] | 过滤出价格低于10的书。 |
| //* | $..* | 所有元素。 |

```python
import json
import jsonpath

obj = json.load(open('4_解析_jsonpath.json','r',encoding='utf-8'))

# 书店所有书的作者
author_list = jsonpath.jsonpath(obj, '$.store.book[*].author')
print(author_list) # ['Nigel Rees', 'Evelyn Waugh', 'Herman Melville', 'J. R. R. Tolkien']

# 所有的作者
all_author_list = jsonpath.jsonpath(obj, '$..author')
print(all_author_list) # ['Nigel Rees', 'Evelyn Waugh', 'Herman Melville', 'J. R. R. Tolkien', 'Moly']

# store的所有元素。
store_list = jsonpath.jsonpath(obj, '$.store.*')
print(store_list) # [[{'category': 'reference', 'author': 'Nigel Rees', 'title': 'Sayings of the Century', 'price': 8.95}, {'category': 'fiction', 'author': 'Evelyn Waugh', 'title': 'Sword of Honour', 'price': 12.99}, {'category': 'fiction', 'author': 'Herman Melville', 'title': 'Moby Dick', 'isbn': '0-553-21311-3', 'price': 8.99}, {'category': 'fiction', 'author': 'J. R. R. Tolkien', 'title': 'The Lord of the Rings', 'isbn': '0-395-19395-8', 'price': 22.99}], {'color': 'red', 'price': 19.95, 'author': 'Moly'}]

# store里面所有东西的price
price_list = jsonpath.jsonpath(obj, '$.store..price')
print(price_list) # [8.95, 12.99, 8.99, 22.99, 19.95]

# 第三个书
third_book = jsonpath.jsonpath(obj, '$..book[2]')
print(third_book) # [{'category': 'fiction', 'author': 'Herman Melville', 'title': 'Moby Dick', 'isbn': '0-553-21311-3', 'price': 8.99}]

# 最后一本书
last_book = jsonpath.jsonpath(obj, '$..book[(@.length-1)]')
print(last_book) # [{'category': 'fiction', 'author': 'J. R. R. Tolkien', 'title': 'The Lord of the Rings', 'isbn': '0-395-19395-8', 'price': 22.99}]

# 前面的两本书
front_two_book = jsonpath.jsonpath(obj, '$..book[:2]')
print(front_two_book) # [{'category': 'reference', 'author': 'Nigel Rees', 'title': 'Sayings of the Century', 'price': 8.95}, {'category': 'fiction', 'author': 'Evelyn Waugh', 'title': 'Sword of Honour', 'price': 12.99}]

# 过滤出所有的包含isbn的书，条件过滤：括号前+？
isbn_book = jsonpath.jsonpath(obj, '$..book[?(@.isbn)]')
print(isbn_book) # [{'category': 'fiction', 'author': 'Herman Melville', 'title': 'Moby Dick', 'isbn': '0-553-21311-3', 'price': 8.99}, {'category': 'fiction', 'author': 'J. R. R. Tolkien', 'title': 'The Lord of the Rings', 'isbn': '0-395-19395-8', 'price': 22.99}]

# 过滤出价格低于10的书
ten_less_book = jsonpath.jsonpath(obj, '$..book[?(@.price<10)]')
print(ten_less_book) # [{'category': 'reference', 'author': 'Nigel Rees', 'title': 'Sayings of the Century', 'price': 8.95}, {'category': 'fiction', 'author': 'Herman Melville', 'title': 'Moby Dick', 'isbn': '0-553-21311-3', 'price': 8.99}]
```
案例：jsonpath解析淘票票所有城市
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673590423879-a7001700-8c6e-4ba2-a22f-8b334f58e7e1.png#averageHue=%23e7de92&clientId=u7ef700b4-b652-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=737&id=u05cdae39&margin=%5Bobject%20Object%5D&name=image.png&originHeight=737&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=584577&status=done&style=none&taskId=ud326e933-2da1-4a76-80ef-9dd27753c81&title=&width=1920)
```python
import urllib.request
import json
import jsonpath

url = 'https://dianying.taobao.com/cityAction.json?activityId&_ksTS=1673589799176_108&jsoncallback=jsonp109&action=cityAction&n_s=new&event_submit_doGetAllRegion=true'
headers = {
    # 注意要去掉带: 的请求头和accept-encoding
    # ':authority': 'dianying.taobao.com',
    # ':method': 'GET',
    # ':path': '/cityAction.json?city=445100&_ksTS=1673589252339_19&jsoncallback=jsonp20&action=cityAction&n_s=new&event_submit_doLocate=true',
    # ':scheme': 'https',
    'accept': 'text/javascript, application/javascript, application/ecmascript, application/x-ecmascript, */*; q=0.01',
    # 'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'zh-CN,zh;q=0.9',
    'cache-control': 'no-cache',
    'cookie': 'cna=IC7YGjskORoCAbfsE6XxK2bo; thw=cn; t=73e4e60fd329c9ab3a6a41404c16ac7b; cookie2=1df320ad39ea4558bb69a9e4d84c0546; v=0; _tb_token_=37f338e835ea6; xlly_s=1; tb_city=445100; tb_cityName="s7HW3Q=="; isg=BHx8jy4AM8-gCgaJabnUUzOCTRoudSCfiKMLbVb71GdKIR6rfof0LvOQAUlZb1j3; tfstk=ceDhBoYfvXPQIuv-52wBHeqZdzLOZx6zoYksQh-yikGnyXHNiXCNgTdejyYX8_1..; l=fBNEViI4Lul4OFD0BO5BFurza77tUBAbzrVzaNbMiIEGa1lATF1IBNCeMq5e7dtjgT5qAetzEHwxcdUM5u438K9ZrVSzKtyuJgpH8eM3N7AN.',
    'pragma': 'no-cache',
    'referer': 'https://dianying.taobao.com/?spm=a1z21.3046609.city.49.5175112aLzLmFg&city=445100',
    'sec-ch-ua': '"Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"',
    'sec-ch-ua-mobile': '?0',
    'sec-ch-ua-platform': '"Windows"',
    'sec-fetch-dest': 'empty',
    'sec-fetch-mode': 'cors',
    'sec-fetch-site': 'same-origin',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36',
    'x-requested-with': 'XMLHttpRequest'
}

request = urllib.request.Request(url=url, headers=headers)
response = urllib.request.urlopen(request)
content = response.read().decode('utf-8')
# 切割，切两次
content = content.split('(')[1].split(')')[0]
# 写入json文件
with open('5_解析_jsonpath_淘票票.json', 'w', encoding='utf-8') as fp:
    fp.write(content)
# 注意是读文件
obj = json.load(open('5_解析_jsonpath_淘票票.json', 'r', encoding='utf-8'))
# 匹配
city_list = jsonpath.jsonpath(obj, '$..regionName')
# 写入txt文件
with open('5_解析_jsonpath_淘票票_城市.txt', 'w', encoding='utf-8') as fp:
    fp.write(str(city_list))
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673590441272-3e938bcd-3e66-48cc-aa6d-7776625feb96.png#averageHue=%2389734e&clientId=u7ef700b4-b652-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=651&id=ub4fe2031&margin=%5Bobject%20Object%5D&name=image.png&originHeight=651&originWidth=1578&originalType=binary&ratio=1&rotation=0&showTitle=false&size=95235&status=done&style=none&taskId=u3b2e3abe-ea1e-4d5a-a0c7-75f268b81c4&title=&width=1578)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673590455255-9c25d8c0-236d-4f28-8b78-5c74db0baa70.png#averageHue=%235c5930&clientId=u7ef700b4-b652-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=404&id=ud2b09eaa&margin=%5Bobject%20Object%5D&name=image.png&originHeight=404&originWidth=1885&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65593&status=done&style=none&taskId=u07f96f57-77ec-4644-a56c-c3615783e1c&title=&width=1885)
## BeautifulSoup

1. 基本简介
   1. BeautifulSoup 简称bs4
   2. 什么是BeatifulSoup? 美丽的汤？
      1. Beautifulsoup，和xml一样，是一个html的解析器，主要功能也是解析和提取数据
   3. 优缺点?
      1. 缺点：效率没有lxml的效率高
      2. 优点：接口设计人性化，使用方便
2. 安装以及创建
   1. 安装 `**pip install bs4**`
   2. 导入 `**from bs4 import BeautifulSoup**`
   3. 创建对象
      1. 服务器响应的文件生成对象 `**soup = BeautifulSoup(response.read().decode(), 'lxml')**`
      2. 本地文件生成对象 `**soup = BeautifulSoup(open('1.html'), 'lxml')**`
      3. 注意：默认打开文件的编码格式gbk所以需要指定打开编码格式
3. 节点定位
   1. 根据标签名查找节点
      1. `**soup.a**` [注]只能找到第一个a
      2. `**soup.a.name**` 标签名
      3. `**soup.a.attrs**` 标签属性
   2. 函数
      1. find(返回一个对象)
         1. `**find('a')**`：只找到第一个a标签
         2. `**find('a', title='名字')**` 根据title的值找标签对象
         3. `**find('a', class_='名字')**` 根据class的值找标签对象
      2. find all(返回一个列表)
         1. `**find_all('a')**` 查找到所有的a
         2. `**find_all(['a', 'span'])**` 返回所有的a和span
         3. `**find_all('a', limit=2)**` 只找前两个a
      3. select(根据选择器得到节点对象)[推荐]
         1. element eg:`**p**`
         2. class eg:`**.firstname**`
         3. #id eg:`**#firstname**`
         4. 属性选择器 `**[attribute]**`
         5. 层级选择器
            1. element element `**div p **`
            2. element>element `**div>p**`
            3. element, element `**div,p**` eg: `**soup = soup.select('a,span')**`
4. 节点信息
   1. 获取节点内容：适用于标签中嵌套标签的结构
      1. `**obj.string**`
      2. `**obj.get_text()**`[推荐]
   2. 节点的属性
      1. tag.name 获取标签名 eg: `**tag = find('li')**``print(tag.name)`
      2. tag.attrs 将属性值作为一个字典返回
      3. 获取节点属性
         1. `**obj.attrs.get('title')**`[常用]
         2. `**obj.get('title')**`
         3. `**obj['title']**`

基本语法：
```python
from bs4 import BeautifulSoup

# 注意加上编码，默认是gbk
soup = BeautifulSoup(open('6_解析_bs4的基本使用.html', encoding='utf-8'), 'lxml')
# 第一个符合条件的数据
print(soup.a)
# 标签名
print(soup.a.name)
# 标签属性
print(soup.a.attrs)

# bs4的一些函数
# 只找到第一个a标签
print(soup.find('a'))
# 根据title的值找标签对象
print(soup.find('a', title='a2'))
# 根据class的值找标签对象
print(soup.find('a', class_='a1'))

# 查找到所有的a
print(soup.find_all('a'))
# 返回所有的a和span，注意放列表里
print(soup.find_all(['a', 'span']))
# 只找前两个a
print(soup.find_all('a', limit=2))

# select返回列表
print(soup.select('a'))
# 下面的写法和js获取css操作很像
print(soup.select('.a1'))
print(soup.select('li[id]'))
print(soup.select('li[id="l2"]'))
print(soup.select('div li'))
print(soup.select('div>ul'))
print(soup.select('a,span'))

# 获取对象
obj = soup.select('#d1')[0]
print(obj.string)
print(obj.get_text())
obj2 = soup.select('#p1')[0]
print(obj2.name)
print(obj2.attrs.get('class'))
print(obj2.get('class'))
print(obj2['class'])
```
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
    <div>
      <ul>
        <li id="l1">张三</li>
        <li id="l2">李四</li>
        <li>王五</li>
        <a href="" id="" class="a1">尚硅谷</a>
      </ul>
      <a href="" title="a2">百度</a>
      <span>哈哈哈</span>
    </div>
    <div id="d1">
      <span>哈哈哈</span>
    </div>
    <p id="p1" class="p1">呵呵呵</p>
  </body>
</html>
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673594311418-db43e48c-7acc-4500-a22f-1d94cd3108b5.png#averageHue=%232c2c2c&clientId=u7ef700b4-b652-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=741&id=ubadfac77&margin=%5Bobject%20Object%5D&name=image.png&originHeight=741&originWidth=1527&originalType=binary&ratio=1&rotation=0&showTitle=false&size=78760&status=done&style=none&taskId=u467d6f88-17eb-47a8-a3fb-09f333b6c7d&title=&width=1527)
案例：爬取星巴克的咖啡产品和图片的数据
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673596583975-da53b43d-d2a1-4398-a242-e32cc25b2c26.png#averageHue=%23cbb288&clientId=u0a30ed57-5be8-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=700&id=u34019e35&margin=%5Bobject%20Object%5D&name=image.png&originHeight=700&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=257298&status=done&style=none&taskId=uc85a034e-75a9-4cce-94dc-e8fa4ba048d&title=&width=1920)
```python
import urllib.request

url = 'https://www.starbucks.com.cn/menu/'
response = urllib.request.urlopen(url)
content = response.read().decode('utf-8')

from bs4 import BeautifulSoup
soup = BeautifulSoup(content, 'lxml')
# 获取产品名
name_list = soup.select('ul[class="grid padded-3 product"] strong')
# 获取产品列表
src_list = soup.select('ul[class="grid padded-3 product"] div[class="preview circle"]')
for i in range(len(name_list)):
    real_src = "https://www.starbucks.com.cn" + src_list[i].attrs['style'].split('("')[1].split('")')[0]
    # 注意这里有坑，文件名不能有/斜杠
    urllib.request.urlretrieve(url=real_src, filename='./starbucks_img/' + name_list[i].get_text().replace('/', "or") + '.jpg')
```
爽歪歪：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673596433708-15276f2e-afaa-4a6b-a434-122dd861f34b.png#averageHue=%233d4043&clientId=u18ea6fc6-8c5d-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=694&id=u627e64b9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=694&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=393934&status=done&style=none&taskId=udcba2bb8-731e-455c-87d1-40f766e7a89&title=&width=1920)

