## Selenium
1. 什么是selenium?
   1. selenium是一个用于web应用程序测试的工具。
   2. Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。
   3. 支持通过各种driver (FirfoxDriver，IternetExplorerDriver，Operariver，ChromeDriver) 驱动真实浏览器完成测试。
   4. selenium也是支持无界面浏览器操作的。
2. 为什么使用selenium?
   1. 模拟浏览器功能，自动执行网页中的js代码，实现动态加载。
   2. 如京东的秒杀模块，采用模拟浏览器发送请求的方式，无法获取该部分数据。所以需要selenium来模拟浏览器，获取真实数据。
3. 如何安装selenium?
   1. [操作谷歌浏览器驱动下载地址]()
   2. [谷歌驱动和谷歌浏览器版本之间的映射表](http://blog.csdn.net/huilan_same/article/details/51896672) 
   3. 查看谷歌浏览器版本谷歌浏览器右上角-->帮助-->关于
   4. `pip install selenium` 
   5. [解决闪退问题](https://blog.csdn.net/m0_75245302/article/details/128107950)
4. selenium的使用步骤?
   1. 导入: `from selenium import webdriver`
   2. 创建谷歌浏览操作对象：`path = 谷歌浏览器驱动文件路径` `browser = webdriver.Chrome(path)`
   3.  访问网址 `url = 要访问的网址browser.get(url)`
5. selenium的元素定位?
   1. 元素定位：自动化要做的就是模拟鼠标和键盘来操作来操作这些元素，点击、输入等等。操作这些元素前首先要找到它们，webDriver提供很多定位元素的方法。
   2. 方法，注：以下都是旧版本的语法，新版本的见代码：
      1. **find**_**element**_**by **_**id**  eg: `button = browser.find_element_by_id('su')`
      2. **find_elements_by_name** eg: `name = browser.find_element_by_name('wd')`
      3. **find**_**elements**_**by**_**xpath** eg: `xpath1 = browser.find_elements_by_xpath('//input[@id="su"]')`
      4. **find**_**elements_by_tag_name** eg: `names = browser.find_elements_by_tag_name('input')`
      5. **find**_**elements**_**by**_**css**_**selector** eg: `my_input = browser.find_elements_by_css_selector('#kw')[0]`
      6. **find**_**elements**_**by**_**link**_**text** eg: `browser.find_element_by_link_text("新闻")`
6. 访问元素信息
   1. 获取元素属性`.get_attribute('class')`
   2. 获取元素文本 `.text`
   3. 获取id `.id`
   4. 获取标签名 `.tag_name`
7. 交互
   1. 点击：`click()`
   2. 输入：`send_keys()`
   3. 后退操作：`browser.back()`
   4. 前进操作：`browser.forword()`
   5. 模拟JS滚动：
      1. `js = 'document.body.scrollTop=100000'`
      2. `js = 'document.documentElement.scrollTop=100000'`
      3. `browser.execute_script(js)` 执行js代码
   6. 获取网页代码：`page_source`

基本使用：
```python
from selenium import webdriver
# 创建浏览器操作对象
path = 'chromedriver.exe'
browser = webdriver.Chrome(path)
# 访问网站
url = 'https://www.jd.com'
browser.get(url=url)
# 获取网页源码
content = browser.page_source
print(content)
```
元素定位：
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

path = 'chromedriver.exe'
browser = webdriver.Chrome(path)
url = 'https://www.baidu.com'
browser.get(url=url)

# 元素定位
# 根据id找到对象
button1 = browser.find_element(by=By.ID, value='su')
# 根据name
button2 = browser.find_element(by=By.NAME, value='wd')
# 根据xpath
button3 = browser.find_element(by=By.XPATH, value='//input[@id="su"]')
# 根据标签名
button4 = browser.find_element(by=By.TAG_NAME, value='input')
# 根据选择器
button5 = browser.find_element(by=By.CSS_SELECTOR, value='#kw')
# 根据连接文本
text = browser.find_element(by=By.LINK_TEXT, value='新闻')
```
交互操作：
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

path = 'chromedriver.exe'
browser = webdriver.Chrome(path)
url = 'https://www.baidu.com'
browser.get(url=url)

import time
time.sleep(2)
# 获取文本框对象
input = browser.find_element(by=By.ID, value='kw')
# 输入信息
input.send_keys('周杰伦')
time.sleep(2)
# 获取百度一下的按钮
button = browser.find_element(by=By.ID, value='su')
# 点击按钮
button.click()
time.sleep(2)
js = 'document.documentElement.scrollTop=100000'
browser.execute_script(js)
time.sleep(2)
# 获取下一页的按钮
next = browser.find_element(by=By.XPATH, value='//a[@class="n"]')
next.click()
time.sleep(2)
# 回到上一页
browser.back()
time.sleep(2)
# 返回
browser.forward()
time.sleep(2)
# 退出
browser.quit()
```
## Phantomjs
**基本被淘汰了**

1. 什么是Phantomjs?
   1. 是一个无界面的浏览器
   2. 支持页面元素查找，js的执行等
   3.  由于不进行css和gui渲染，运行效率要比真实的浏览器要快很多
2. 如何使用phantomjs?
   1. 获取PhantomJs.exe文件路径path
   2. `**browser = webdriver.PhantomJs(path)**`
   3. `**browser.get(url)**`
   4. 扩展: 保存屏幕快照：`browser.save_screenshot("baidu.png')`
## Chrome handless
Chrome-headless 模式，Google 针对 Chrome 浏览器 59版 新增加的一种模式，可以让你不打开UI界面的情况下使用 Chrome 浏览器，所以运行效果与 Chrome 保持完美一致。

1. 系统要求:

chrome
Unix\Linux 系统需要 chrome >= 59
Windows 系统需要 chrome >= 60
Python3.6
Selenium==3.4.*
ChromeDriver==2.31

2. 配置：
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

chrome_options = Options()
chrome_options.add_argument('--headless')
chrome_options.add_argument('-disable-gpu')

# 自己电脑chrome的路径
path = r'C:\Program Files(x86)\GooglelChrome\Application\chrome.exe'
chrome_options.binary_location = path

browser = webdriver.Chrome(options=chrome_options)
browser.get('https://www.baidu.com/')
```

3. 配置封装：
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# 封装的handless
def share_browser():
    chrome_options = Options()
    chrome_options.add_argument('--headless')
    chrome_options.add_argument('-disable-gpu')
    path = r'C:\Program Files\Google\Chrome\Application\chrome.exe'
    chrome_options.binary_location = path
    browser = webdriver.Chrome(options=chrome_options)
    return browser

browser = share_browser()
browser.get('https://www.baidu.com/')
browser.save_screenshot('baidu.png')
```
