与urllib操作相似
> Requests 唯一的一个**非转基因**的 Python HTTP 库，人类可以安全享用。

## 基本使用

1. 文档：
   1. [官方文档](https://cn.python-requests.org/zh_CN/latest/)
   2. [快速上手](http://cn.python-requests.org/zh_CN/latest/user/quickstart.html)
2. 安装：`pip install requests -i https://pypi.douban.com/simple`
3. response的属性以及类型
   1. 类型：`models.Response`
   2. `r.text` 获取网站源码
   3. `r.encoding` 访问或定制编码方式
   4. `r.url` 获取请求的url
   5. `r.content` 响应的字节类型
   6. `r.status_code` 响应的状态码
   7. `r.headers` 响应的头信息
```python
import requests

url = 'https://www.baidu.com'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'
}
response = requests.get(url=url, headers=headers)
# 设置编码格式
response.encoding = 'utf-8'

# 一个类型和六个属性
print(type(response))
# 以字符串的形式返回原来的网页源码
print(response.text)
# 返回url地址
print(response.url)
# 返回二进制数据
print(response.content)
# 返回响应状态码
print(response.status_code)
# 返回响应头
print(response.headers)
```
## get请求
`requests.get()`
```python
import requests

url = 'https://www.baidu.com/s?'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'
}
data = {
    'wd': '北京'
}
# url 请求资源路径
# params 参数
# kwargs 字典
response = requests.get(url=url, params=data, headers=headers)
response.encoding = 'utf-8'
content = response.text
print(content)
```
总结：

1. 参数使用params传递
2. 参数无需urlencode编码
3. 不需要请求对象的定制
4. 请求资源路径中的？可以加也可以不加
## post请求
`requests.post()`
还是百度翻译案例：
```python
import requests
import json
url = 'https://fanyi.baidu.com/v2transapi'
headers = {
    'Acs-Token': ...,
    'Cookie': ...,
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}
data = {
    'from': 'en',
    'to': 'zh',
    'query': 'selenium',
    'transtype': 'realtime',
    'simple_means_flag': '3',
    'sign': '571873.809680',
    'token': 'b2a75f516a891e995777e3ae1b686a0d',
    'domain': 'common'
}
# url 请求资源路径
# data 请求对象
# kwargs 字典
response = requests.post(url=url, data=data, headers=headers)
content = response.text
obj = json.loads(content)
print(obj)
```
get和post区别?

1. get请求的参数名字是params，post请求的参数的名字是data
2. 请求资源路径后面可以不加`?`
3. 不需要手动编解码
4. 不需要做请求对象的定制
## 代理
```python
import requests

url = 'http://www.baidu.com/s?'
headers = {
    'Cookie': ...,
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'
}
data = {
    'wd': 'ip'
}
proxy = {
    'http': '140.255.139.249:3256'
}
response = requests.get(url=url, params=data, headers=headers)
response.encoding = 'utf-8'
content = response.text
with open('agent.html', 'w', encoding='utf-8') as fp:
    fp.write(content)
```
## cookie定制
案例：自动登入古诗文网
难点：隐藏域、验证码
拓展：自动识别验证码脚本的使用
```python
# 发现__VIEWSTATE __VIEWSTATEGENERATOR code是一个可以变化的量
# 难点：1. __VIEWSTATE __VIEWSTATEGENERATOR , 但观察到这两个数据在页面源码中 我们需要获取页面源码 然后解析就可以获取这两个值
#      2.验证码

# 获取页面源码
import requests
url = 'https://so.gushiwen.cn/user/login.aspx?from=http://so.gushiwen.cn/user/collect.aspx'
headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'
}
response = requests.get(url=url, headers=headers)
content = response.text

# 解析页面源码
from bs4 import BeautifulSoup
soup = BeautifulSoup(content, 'lxml')
view_state = soup.select('#__VIEWSTATE')[0].attrs.get('value')
view_state_generator = soup.select('#__VIEWSTATEGENERATOR')[0].attrs.get('value')
code = soup.select('#imgCode')[0].attrs.get('src')
code_url = 'https://so.gushiwen.cn' + code

# 下载验证码识别插件：pip install ddddocr 记得进入源码删掉广告
import ddddocr
ocr = ddddocr.DdddOcr()
# 注意这里有坑，保持前后请求的一致，否则会造成验证码不一致
# 采用request session()解决 通过session的返回值 就能使用请求变成一个对象
session = requests.session()
response_code = session.get(code_url)
# 注意此时要使用二进制数据
content_code = response_code.content
# 保存图片,wb:将二进制数据写入文件
with open('code.png', 'wb') as f:
    f.write(content_code)
# 读取图片
with open('code.png', 'rb') as f:
    img_byte = f.read()
    code_text = ocr.classification(img_byte)

# 点击登录
url_post = 'https://so.gushiwen.cn/user/login.aspx?from=http%3a%2f%2fso.gushiwen.cn%2fuser%2fcollect.aspx'
data_post = {
    '__VIEWSTATE': view_state,
    '__VIEWSTATEGENERATOR': view_state_generator,
    'from':' http://so.gushiwen.cn/user/collect.aspx',
    'email': '...',
    'pwd': '...',
    'code': code_text,
    'denglu': '登录'
}
# 注意这里要用session，保持请求为同一个
response_post = session.post(url=url_post, data=data_post, headers=headers)
content_post = response_post.text
with open('gushiwen.html', 'w', encoding='utf-8') as fp:
    fp.write(content_post)
```
也可以用老师讲到的超级鹰打码平台，这里就不操作了...

