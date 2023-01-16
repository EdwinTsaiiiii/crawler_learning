## urllib库的基本使用
`urllib.request.urlopen()` 模拟浏览器向服务器发送请求
response 服务器返回的数据
response的数据类型是**HttpResponse**
字节-->字符串：解码decode
字符串-->字节：编码encode
使用urllib获取百度首页的源码：
```python
# 使用urllib获取百度首页的源码
import urllib.request

# 1.定义url，指的是访问的地址
url = 'http://www.baidu.com/'

# 2.模拟浏览器向服务器发送请求
response = urllib.request.urlopen(url)

# 3.获取响应中的页面源码
# read方法，返回的是字节形式的二进制数据
# 我们要将二进制的数据转换为字符串（解码） decode()
content = response.read().decode('utf-8')

# 4.打印数据
print(content)
```
## urllib库的基本方法
`read()`：字节形式读取二进制，扩展: rede(5)返回前几个字节
`readline()`：读取一行
`readlines()`：一行一行读取 直至结束
`getcode()`：获取状态码
`geturl()`：获取ur1
`getheaders()`：获取headers
`urllib.request.urlretrieve()`：请求网页、请求图片、请求视频
```python
import urllib.request

# 下载网页
url_page = 'http://www.baidu.com/'
# url代表的是下载的路径，filename是文件的名字
urllib.request.urlretrieve(url=url_page,filename='baidu.html')

# 下载图片
url_img = 'https://pics1.baidu.com/feed/7acb0a46f21fbe091b3b35feedec80388644ad0f.jpeg@f_auto?token=1f93ad37614106d32ebda8b1c569b9f4'
urllib.request.urlretrieve(url_img,'trump.jpg')

# 下载视频(先f12做下定位，B站有反爬虫，抖音的就可以爬)
url_video = 'https://v26-web.douyinvod.com/9a35f40b6dec1506c40edaddd3913221/63be609a/video/tos/cn/tos-cn-ve-15/owmCfA2nI9RA4P2tDMAnhD4Hh6emuO8ePXCfEQ/?a=6383&ch=0&cr=0&dr=0&cd=0%7C0%7C0%7C0&cv=1&br=1096&bt=1096&cs=0&ds=4&ft=LjhJEL998xl8uEe~D0P5H4eaciDXtK8e~5QEe8QN8SLD1Ini&mime_type=video_mp4&qs=0&rc=ZDo6aDU2aWU3Nzc4aWc6OUBpM3M6ZDo6Zmt5aDMzNGkzM0AwXjAyMWItXmMxYmAtYDUtYSNrLmNscjRnbXJgLS1kLS9zcw%3D%3D&btag=28000'
urllib.request.urlretrieve(url_video,'ikun.mp4')

```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673417393302-1a5487b0-ed04-4bad-af24-a39cfeaf6137.png#averageHue=%23383f45&clientId=u8bf9e77a-8421-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=141&id=ue51e8be0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=141&originWidth=270&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7907&status=done&style=none&taskId=u6b5bfb20-00b4-45d6-9adf-e4d24dfc7f0&title=&width=270)
## 请求对象的定制
url的组成：[https://www.baidu.com/s?wd=%E5%91%A8%E6%9D%B0%E4%BC%A6](https://www.baidu.com/s?wd=%E5%91%A8%E6%9D%B0%E4%BC%A6)

| `[http](http://www.baidu.com)/[http](http://www.baidu.com)s` | `[www.baidu.com](http://www.baidu.com)` | `80/443` | `s` | `wd = [%E5%91%A8%E6%9D%B0%E4%BC%A6](https://www.baidu.com/s?wd=%E5%91%A8%E6%9D%B0%E4%BC%A6)` | `#` |
| --- | --- | --- | --- | --- | --- |
| 协议	 | 主机 | 端口号 | 路径 | 参数 | 锚点 |

UA介绍：User Agent中文名为用户代理，简称 UA，它是一个特殊符串头，使得服务器能够识别客户使用的操作系统及版本、CPU 类型、浏览器及版本。浏览器内核、浏览器染引擎、浏览器语言、浏览器插件等。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673418273318-1eedf4a7-cddb-4430-95e1-7aefbdde7719.png#averageHue=%23ceb282&clientId=u23b4036b-3e09-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=937&id=ua785180e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=937&originWidth=1377&originalType=binary&ratio=1&rotation=0&showTitle=false&size=231155&status=done&style=none&taskId=ub7567852-3955-4591-bbf6-b1e238fd1ee&title=&width=1377)
```python
import urllib.request
# 遇到反爬，访问的时候数据不完整
url = 'https://www.baidu.com/'
response = urllib.request.urlopen(url)
content = response.read().decode('utf-8')
print(content)
```
定制请求对象，设置相应的用户代理，解决一些反爬虫手段
语法：`request = urllib.request.Request()`
```python
import urllib.request
url = 'https://www.baidu.com/'
# 进行用户代理
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/'}
# 请求对象的定制
request = urllib.request.Request(url=url,headers=headers) 
# 再进行请求
response = urllib.request.urlopen(request)
content = response.read().decode('utf-8')
print(content)
```
扩展：编码的由来（略），可参考一下视频：[传送门](https://www.bilibili.com/video/BV1xD4y1y7yc/?spm_id_from=333.999.0.0&vd_source=3aa9a54e7578504ee2eeb1405d438eff)
## 编解码
### get请求方式：`urllib.parse.quote()`
```python
import urllib.request
import urllib.parse

# UnicodeEncodeError: 'ascii' codec can't encode characters in position 10-12: ordinal not in range(128)
# url = 'https://www.baidu.com/s?wd=周杰伦'

# 将周杰伦三个字变为unicode编码
# 需要urllib.parse库
name = urllib.parse.quote('周杰伦')
url = 'https://www.baidu.com/s?wd=' + name
# 请求对象定制
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'}
request = urllib.request.Request(url=url,headers=headers)
# 模拟浏览器向服务器发送请求
response = urllib.request.urlopen(request)
# 获取响应内容
content = response.read().decode('utf-8')
print(content)
```
### get请求方式：`urllib.parse.urlencode()`
```python
import urllib.request
import urllib.parse
# urlencode应用场景是多个参数的时候
# 'https://www.baidu.com/s?wd=周杰伦&sex=男&location=中国台湾'

# 基础路径
base_url = 'https://www.baidu.com/s?'
data = {
    'wd':'周杰伦',
    'sex':'男',
    'location':'中国台湾'
}
# 编码
new_data = urllib.parse.urlencode(data)
# 请求资源路径
url = base_url + new_data
# 请求对象定制
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'}
request = urllib.request.Request(url=url,headers=headers)
# 模拟浏览器向服务器发送请求
response = urllib.request.urlopen(request)
# 获取响应内容，解码
content = response.read().decode('utf-8')
print(content)
```
### post请求方式
**拿百度翻译做案例**，先查找翻译对应的接口
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673421661008-4ad60151-9c1e-4541-b6fa-1d0ac2e94427.png#averageHue=%23f0d38e&clientId=u23b4036b-3e09-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=699&id=u4714c394&margin=%5Bobject%20Object%5D&name=image.png&originHeight=699&originWidth=1150&originalType=binary&ratio=1&rotation=0&showTitle=false&size=89958&status=done&style=none&taskId=u303ea82b-ca02-43fe-9862-bcd84eb41a7&title=&width=1150)
**关于报错：**`{"errno":997,"errmsg":"\u672a\u77e5\u9519\u8bef","query":"fetch","from":"en","to":"zh","error":997}`
**需要我们添加cookie**
```python
headers = {
    'Cookie':'PSTM=1649489308; BAIDUID=4AD990F08D65F21D9CE3B75FA022D48E:FG=1; BIDUPSID=1D9101783A84F8EF463B71C95AA96696; BDUSS_BFESS=WRhV1lVaUJSckdqczhydmpVVlNIdXJIM0NMNVJCYnBnc3VjNmxHakd2cU5DYWxqSUFBQUFBJCQAAAAAAAAAAAEAAAAb7DRgQWNvdXNtYTIwMTUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAI18gWONfIFjc; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; BA_HECTOR=818k0gaka1ahag2k0h8k01bb1hrqplf1k; BAIDUID_BFESS=4AD990F08D65F21D9CE3B75FA022D48E:FG=1; delPer=0; ZFY=xP4o1V:Ay0gLRfW6ZqlhLLG:AzDY5M9:Af4EtmdsGNYR4g:C; PSINO=6; ariaDefaultTheme=undefined; RT="z=1&dm=baidu.com&si=jw91jr8kk8c&ss=lcr8yx74&sl=c&tt=pgs&bcn=https://fclog.baidu.com/log/weirwood?type=perf&ld=2s9y&ul=3rv8&hd=3rw4"; BDRCVFR[feWj1Vr5u3D]=mk3SLVN4HKm; H_PS_PSSID=36545_37973_37647_37556_38019_36920_36804_37927_38040_26350_37881; APPGUIDE_10_0_2=1; REALTIME_TRANS_SWITCH=1; FANYI_WORD_SWITCH=1; HISTORY_SWITCH=1; SOUND_SPD_SWITCH=1; SOUND_PREFER_SWITCH=1; Hm_lvt_64ecd82404c51e03dc91cb9e8c025574=1673421234; ab_sr=1.0.1_MTkwNDllMTg0YTI0MDk0NGEzMjg5MGE4YTQwM2ZlN2E4YmNiMmJhYTdhMGU2M2VmOGM1MmNkZTM3ZDVkMWFhMWM0OTI1MjhiNzU2NTE2M2U3MWRiY2QyMTlmZGNkMjZiZjk0OGJiYjdiZDkwMjI0NGNiZDE2YjAzNWYwMGEzMmIxYjBmZDcwZDVkZWNjZDJkZjhiMjNmMjUzYmU3ZjU3NzExMDEwZGE2MDc0YmJhZTM1YjE4MzgyMzhjOWMwYjBm; Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574=1673421696',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}
```
整体代码如下：
```python
import urllib.request
import urllib.parse
# post请求方式
url = 'https://fanyi.baidu.com/v2transapi?from=en&to=zh'
headers = {
    'Cookie':'PSTM=1649489308; BAIDUID=4AD990F08D65F21D9CE3B75FA022D48E:FG=1; BIDUPSID=1D9101783A84F8EF463B71C95AA96696; BDUSS_BFESS=WRhV1lVaUJSckdqczhydmpVVlNIdXJIM0NMNVJCYnBnc3VjNmxHakd2cU5DYWxqSUFBQUFBJCQAAAAAAAAAAAEAAAAb7DRgQWNvdXNtYTIwMTUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAI18gWONfIFjc; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; BA_HECTOR=818k0gaka1ahag2k0h8k01bb1hrqplf1k; BAIDUID_BFESS=4AD990F08D65F21D9CE3B75FA022D48E:FG=1; delPer=0; ZFY=xP4o1V:Ay0gLRfW6ZqlhLLG:AzDY5M9:Af4EtmdsGNYR4g:C; PSINO=6; ariaDefaultTheme=undefined; RT="z=1&dm=baidu.com&si=jw91jr8kk8c&ss=lcr8yx74&sl=c&tt=pgs&bcn=https://fclog.baidu.com/log/weirwood?type=perf&ld=2s9y&ul=3rv8&hd=3rw4"; BDRCVFR[feWj1Vr5u3D]=mk3SLVN4HKm; H_PS_PSSID=36545_37973_37647_37556_38019_36920_36804_37927_38040_26350_37881; APPGUIDE_10_0_2=1; REALTIME_TRANS_SWITCH=1; FANYI_WORD_SWITCH=1; HISTORY_SWITCH=1; SOUND_SPD_SWITCH=1; SOUND_PREFER_SWITCH=1; Hm_lvt_64ecd82404c51e03dc91cb9e8c025574=1673421234; ab_sr=1.0.1_MTkwNDllMTg0YTI0MDk0NGEzMjg5MGE4YTQwM2ZlN2E4YmNiMmJhYTdhMGU2M2VmOGM1MmNkZTM3ZDVkMWFhMWM0OTI1MjhiNzU2NTE2M2U3MWRiY2QyMTlmZGNkMjZiZjk0OGJiYjdiZDkwMjI0NGNiZDE2YjAzNWYwMGEzMmIxYjBmZDcwZDVkZWNjZDJkZjhiMjNmMjUzYmU3ZjU3NzExMDEwZGE2MDc0YmJhZTM1YjE4MzgyMzhjOWMwYjBm; Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574=1673421696',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}
data = {
    'from': 'en',
    'to': 'zh',
    'query': 'fetch',
    'simple_means_flag': '3',
    'sign': '999199.777262',
    'token': 'b2a75f516a891e995777e3ae1b686a0d',
    'domain': 'common',
}
# 对post请求参数进行编码
data = urllib.parse.urlencode(data).encode('utf-8')
# 定制请求对象，注意post请求参数要写在data中
request = urllib.request.Request(url=url,data=data,headers=headers)
# 模拟浏览器发送请求
response = urllib.request.urlopen(request)
# 获取响应内容
content = response.read().decode('utf-8')
print(content)
```
**小技巧：**pycharm给字典的键值自动增加引号
ctrl+r，第一行：`(.*?): (.*)`， 第二行：`'$1': '$2',`
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673423888836-0d80d52a-b3fc-45db-bf92-840048aa008f.png#averageHue=%236a774d&clientId=uefea8c5d-bac2-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=266&id=u94f87954&margin=%5Bobject%20Object%5D&name=image.png&originHeight=266&originWidth=870&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28042&status=done&style=none&taskId=ud560d119-2458-4413-ad8b-3cbee4411ef&title=&width=870)
## ajax
### get请求
案例：爬取豆瓣的电影数据
```python
import json
import urllib.request

# get请求，获取豆瓣电影的第一页数据，并保存起来
url = 'https://m.douban.com/rexxar/api/v2/movie/recommend?refresh=0&start=0&count=20&selected_categories=%7B%22%E7%B1%BB%E5%9E%8B%22:%22%E5%8A%A8%E4%BD%9C%22%7D&uncollect=false&tags=%E5%8A%A8%E4%BD%9C'

headers = {
    'Accept': 'application/json, text/plain, */*',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive',
    'Cookie': 'bid=D4UiEMAOr6E; douban-fav-remind=1; ll="118281"; __gads=ID=6c56e86b3d902ef2-2273401a07d90036:T=1671982708:RT=1671982708:S=ALNI_MbKPAWMLA25PDFbzCX4LRy4TIrfEw; ap_v=0,6.0; __utma=30149280.578607714.1652977725.1671982709.1673491347.5; __utmb=30149280.0.10.1673491347; __utmc=30149280; __utmz=30149280.1673491347.5.2.utmcsr=baidu|utmccn=(organic)|utmcmd=organic; __gpi=UID=00000b978124c257:T=1671982708:RT=1673491473:S=ALNI_MZ35hVfuUod8sULr4dKKyCJgn69dg',
    'Host': 'm.douban.com',
    'Origin': 'https://movie.douban.com',
    'Pragma': 'no-cache',
    'Referer': 'https://movie.douban.com/explore',
    'sec-ch-ua': '"Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"',
    'sec-ch-ua-mobile': '?0',
    'sec-ch-ua-platform': '"Windows"',
    'Sec-Fetch-Dest': 'empty',
    'Sec-Fetch-Mode': 'cors',
    'Sec-Fetch-Site': 'same-site',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}

# 请求对象的定制
request = urllib.request.Request(url=url,headers=headers)
# 模拟浏览器发送请求
response = urllib.request.urlopen(request)
# 获取响应内容
content = response.read().decode('utf-8')
# 转化为字典格式，截取目标数据
data = json.loads(content)['items']

# 下载数据到本地,以下两个方法都是将str格式的数据写入json文件
# 注意open()默认情况是使用gbk编码
# fp = open('douban.json','w',encoding='utf-8')
# fp.write(content)
# with open('douban.json','w',encoding='utf-8') as fp:
#     fp.write(content)

# 我们采用这个json.dumps()，直接将json对象转化为str类型再写入
with open('douban.json','w',encoding='utf-8') as fp:
    # 注意此处设置ensure_ascii=False，防止显示的中文为unicode编码
    fp.write(json.dumps(data, ensure_ascii=False))
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673508092099-7753dd73-b873-4a49-8f3a-f3fb3bc95c86.png#averageHue=%232c2c2b&clientId=ua02825ff-1c61-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=695&id=u0d7d19b8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=695&originWidth=1362&originalType=binary&ratio=1&rotation=0&showTitle=false&size=95175&status=done&style=none&taskId=u2c2dc6bf-e22e-4a2a-bb83-1f54e3ec1b1&title=&width=1362)
案例：爬取豆瓣电影的n页数据：
```python
import json
import urllib.request
import urllib.parse

# 创建request
def create_request(page):
    base_url = 'https://m.douban.com/rexxar/api/v2/movie/recommend?refresh=0&selected_categories=%7B%22%E7%B1%BB%E5%9E%8B%22:%22%E5%8A%A8%E4%BD%9C%22%7D&uncollect=false&tags=%E5%8A%A8%E4%BD%9C'
    headers = {
        'Accept': 'application/json, text/plain, */*',
        'Accept-Language': 'zh-CN,zh;q=0.9',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
        'Cookie': 'bid=D4UiEMAOr6E; douban-fav-remind=1; ll="118281"; __gads=ID=6c56e86b3d902ef2-2273401a07d90036:T=1671982708:RT=1671982708:S=ALNI_MbKPAWMLA25PDFbzCX4LRy4TIrfEw; ap_v=0,6.0; __utma=30149280.578607714.1652977725.1671982709.1673491347.5; __utmb=30149280.0.10.1673491347; __utmc=30149280; __utmz=30149280.1673491347.5.2.utmcsr=baidu|utmccn=(organic)|utmcmd=organic; __gpi=UID=00000b978124c257:T=1671982708:RT=1673491473:S=ALNI_MZ35hVfuUod8sULr4dKKyCJgn69dg',
        'Host': 'm.douban.com',
        'Origin': 'https://movie.douban.com',
        'Pragma': 'no-cache',
        'Referer': 'https://movie.douban.com/explore',
        'sec-ch-ua': '"Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"',
        'sec-ch-ua-mobile': '?0',
        'sec-ch-ua-platform': '"Windows"',
        'Sec-Fetch-Dest': 'empty',
        'Sec-Fetch-Mode': 'cors',
        'Sec-Fetch-Site': 'same-site',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
    }
    data = {
        'start': (page - 1) * 20,
        'count': 20
    }
    data = urllib.parse.urlencode(data)
    url = base_url + '&' + data
    request = urllib.request.Request(url=url, headers=headers)
    return request


# 获取响应内容
def get_data(request):
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf-8')
    data = json.loads(content)['items']
    return data


# 下载数据
def down_load(page, data):
    with open('douban_' + str(page) + '.json','w',encoding='utf-8') as fp:
        fp.write(json.dumps(data, ensure_ascii=False))


# 下载豆瓣电影前十页的数据
if __name__ == '__main__':
    start_page = int(input('请输入起始的页码：'))
    end_page = int(input('请输入结束的页码：'))
    for page in range(start_page, end_page + 1):
        # 为每一页定制请求对象
        request = create_request(page)
        # 获取响应数据
        data = get_data(request)
        # 下载
        down_load(page, data)
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673508025012-a0309ee3-bb23-4f34-a902-df2c5e5ef190.png#averageHue=%23787a50&clientId=ua02825ff-1c61-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=693&id=ub7d49ccc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=693&originWidth=1502&originalType=binary&ratio=1&rotation=0&showTitle=false&size=138769&status=done&style=none&taskId=ub11b1b8b-d13e-493b-897a-2199f564814&title=&width=1502)
### post请求
案例：爬取某城市的肯德基餐厅信息（这次不按页码爬，直接爬取所有）
```python
import json
import urllib.request
import urllib.parse


def crate_request(city, page, limit):
    base_url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname'
    headers = {
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
    }
    form_data = {
        'cname': city,
        'pid': '',
        'pageIndex': page,
        'pageSize': limit
    }
    # 此处注意post请求要将请求参数进行编码
    form_data = urllib.parse.urlencode(form_data).encode('utf-8')
    request = urllib.request.Request(url=base_url, headers=headers, data=form_data)
    return request


def get_total(request):
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf-8')
    # 在字典数据中获取总条数
    total = json.loads(content)['Table'][0]['rowcount']
    return total


def get_data(request):
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf-8')
    data = json.loads(content)['Table1']
    return data


def down_load(data, city):
    with open('kfc_' + city + '.json','w',encoding='utf-8') as fp:
        fp.write(json.dumps(data, ensure_ascii=False))


if __name__ == '__main__':
    city = str(input('请输入城市名：'))
    # 先预请求获取总条数
    request_first = crate_request(city,1,10)
    total = get_total(request_first)
    # 请求对象定制
    request = crate_request(city,1,total)
    # 获取目标数据
    data = get_data(request)
    # 下载
    down_load(data,city)
```
爽歪歪：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673508224205-13e5fff0-3177-4838-913f-c8c3b6550efa.png#averageHue=%23788d61&clientId=ua02825ff-1c61-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=682&id=u46fe3fba&margin=%5Bobject%20Object%5D&name=image.png&originHeight=682&originWidth=1000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=107423&status=done&style=none&taskId=u822d6125-c2f8-45af-b119-a35124abc8b&title=&width=1000)
## URLError\HTTPError
简介：

1. HTTPError类是URLError类的子类
2. 导入的包`**urllib.error.HTTPError**`,`**urllib.error.URLError**`
3. http错误: http错误是针对浏览器无法连接到服务器而增加出来的错误提示。引导并告诉浏览者该页是哪里出了问题。
4. 通过urllib发送请求的时候，有可能会发送失败，这个时候如果想让你的代码更加的健壮，可以通过`try-except`进行捕获异常，异常有两类，`**URLError**`\`**HTTPError**`
```python
import urllib.request
import urllib.error

# url = 'https://blog.csdn.net/fyfugoyfa/article/details/1223738561' # HTTP错误
url = 'http://goudan111.com' # URL错误！

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}

try:
    request = urllib.request.Request(url=url, headers=headers)
    response = urllib.request.urlopen(request)
    content = response.read().decode('utf-8')
    print(content)
except urllib.error.HTTPError:
    print('HTTP错误！')
except urllib.error.URLError:
    print('URL错误！')
```
## Cookie登录
适用场景：数据采集的时候，需要绕过登录，然后进入某个页面
> 个人信息页编码是utf-8，但是还是报了编码错误：**因为并没有进入到个人信息页面，而是跳转到登录页面，登录页面不是utf-8，所以报错。这也是一种反爬手段。**

**请求头信息不够，所以访问不成功（cookie）**
**如QQ空间等网页，带有cookie就可以抓取成功，但还有一种反爬手段，采用动态cookie**
```python
import urllib.request

url = 'https://weibo.com/p/1005057002980837/info'
headers = {
    # cookie中携带者你的登录信息，如果登录之后有cookie，那么我们就可以携带者cookie进入任何页面
    'cookie': ...,
    # referer：判断当前路径是不是由上一个路径进来的，也称作防盗链。
    # 一般情况下，是做图片的防盗链，不是从这个页面进来的，就不让下载。
    # 从前端角度讲，跟路由守卫的作用一样。
    # 注：2023.1微博的请求头没有带这一字段，与其相似的字段是:authority: weibo.com(权限)
    # 'referer':'https://weibo.cn',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36',
}

request = urllib.request.Request(url=url, headers=headers)
response = urllib.request.urlopen(request)
content = response.read().decode('utf-8')
with open('weibo.html', 'w', encoding='utf-8') as fp:
    fp.write(content)
```
爬取下来的个人信息页：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673511301562-2448c558-05bb-4daa-a0b6-a5b34e1b7da1.png#averageHue=%23adccce&clientId=u0f3a8d7a-a300-4&crop=0&crop=0&crop=1&crop=0.9576&from=paste&height=728&id=u0ac6310d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=728&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=643665&status=done&style=none&taskId=u296ea5c0-1970-4174-878c-6ee9cb067a2&title=&width=1920)
## Handler处理器
**为什么要学习handler?**
`urllib.request.urlopen(url)`：不能定制请求头
`urllib.request.Request(url, headers, data)`：可以定制请求头
**Handler：**定制更高级的请求头。随着业务逻辑的复杂，请求对象的定制已经满足不了我们的需求**(动态cookie和代理不能使用请求对象的定制)。**
**案例：**使用handler访问百度, 获取网页源码
```python
import urllib.request

url = 'https://www.baidu.com'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}

request = urllib.request.Request(url=url,headers=headers)
# 获取handler对象
handler = urllib.request.HTTPHandler()
# 获取opener对象
opener = urllib.request.build_opener(handler)
# 调用open方法
response = opener.open(request)
content = response.read().decode('utf-8')
print(content)
```
## 代理服务器

1. 代理的常用功能?
   1. 突破自身IP访问限制，访问国外站点
   2. 访问一些单位或团体内部资源
      1. 扩展：某大学FTP(前提是该代理地址在该资源的允许访问范围之内)，使用教育网内地址段免费代理服务器，就可以用于对教育网开放的各类FTP下载上传，以及各类资料查询共享等服务。
   3. 提高访问速度
      1. 扩展：通常代理服务器都设置一个较大的硬盘缓冲区，当有外界的信息通过时，同时也将其保存到缓冲区中，当其他用户再访问相同的信息时， 则直接由缓冲区中取出信息，传给用户，以提高访问速度。
   4. 隐藏直实IP
      1. 扩展：上网者也可以通过这种方法隐藏自己的IP，免受攻击
2. 代码配置代理
   1. 创建**Reuqest**对象
   2. 创建**ProxyHandler**对象
   3. 用**handler**对象创建**opener**对象
   4. 使用**opener.open**函数发送请求

查询本机ip：直接百度
### 一般代理操作
原来的地址：
![1673513494820.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673513498958-8b56ffd0-5c73-4d15-8c9a-7a675ac709ab.png#averageHue=%23fdfcfc&clientId=u19164f9d-5cec-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=337&id=uc211ffaa&margin=%5Bobject%20Object%5D&name=1673513494820.png&originHeight=337&originWidth=877&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25118&status=done&style=none&taskId=u74610d72-b399-42d0-8b58-c77b8389b90&title=&width=877)
快代理：查询免费代理网址，[https://www.kuaidaili.com/free/](https://www.kuaidaili.com/free/)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33639422/1673513349439-ff99761a-705d-43c4-bb1b-3e4ed95c2314.png#averageHue=%23d9d9d8&clientId=u19164f9d-5cec-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=418&id=u6eb1e8a1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=418&originWidth=1260&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50721&status=done&style=none&taskId=u82ab5a24-d05f-4e4a-9254-cbbfba9e271&title=&width=1260)
```python
import urllib.request

url = 'https://www.baidu.com/s?wd=ip'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}

request = urllib.request.Request(url=url,headers=headers)
# 代理ip，以字典的形式存在
proxies = {
    'https':'210.5.10.87:53281'
}
handler = urllib.request.ProxyHandler(proxies=proxies)
opener = urllib.request.build_opener(handler)
response = opener.open(request)
content = response.read().decode('utf-8')
with open('agent.html','w',encoding='utf-8') as fp:
    fp.write(content)

```
经过代理操作后：
注：免费的服务器太拉了，没有一个能成功代理
### 代理池操作
```python
import urllib.request
import random

url = 'https://www.baidu.com/s?wd=ip'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'
}

request = urllib.request.Request(url=url,headers=headers)
# 代理池
proxies_pool = [
    {'https': '210.5.10.87:53281'},
    {'https': '202.109.157.65:9000'},
    {'https': '121.13.252.62:41564'},
    {'https': '112.14.47.6:52024'},
    {'https': '121.13.252.58:41564'}
]
# 随机抽取一个代理ip
proxies = random.choice(proxies_pool)
handler = urllib.request.ProxyHandler(proxies=proxies)
opener = urllib.request.build_opener(handler)
response = opener.open(request)
content = response.read().decode('utf-8')
with open('agent.html','w',encoding='utf-8') as fp:
    fp.write(content)
```
