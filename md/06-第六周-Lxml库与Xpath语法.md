# 专栏第六周 Lxml库与Xpath语法

Lxml库是基于libxml2的XML解析库的Python封装。该模块使用C语言编写，解析速度比BeautifulSoup更快。这个库使用Xpath语法解析定位网页数据。通过本篇文章你将学到以下内容：

> - Lxml库的使用
> - Xpath相关语法

## Lxml库的安装与使用方法

1. 安装：

   和其他第三方库的安装方法一样，在命令行中运行以下代码：

   ```shell
   pip install lxml
   ```

   如果在安装过程中提示缺少库，请利用搜索引擎查找解决方法。

2. Lxml库的使用

   - 修正HTML代码

     ```python
     from lxml import etree
     
     html_text = '''
     <div>
         <ul>
             <li class="one">one</li>
             <li class="two">two
         </ul>
     </div>
     '''
     html = etree.HTML(html_text)
     print(html) # Lxml库解析数据为Element对象
     result = etree.tostring(html)
     print(result) # 可以自动修正HTML内容
     ```

     ![1567856337964](https://github.com/ai-union/PythonSpyder/blob/master/img/1567856337964.png?raw=true)

     从打印结果我们可以看出，etree库把HTML文档解析为Element对象，并且可以自动修正HTML文档。细心的朋友可能已经发现，我代码中的最后的li标签是不完整的，而从打印结果可以看到，不但li标签补全了，还把html和body标签也自动添加上了。

   - 读取HTML文件

     我们在本地同级目录下面新建一个html_demo.html文件。内容如下：

     ```html
     <html>
         <head>
             <title>DemoHTML</title>
         </head>
         <body>
             <div>
                 <ul>
                     <li>a</li>
                     <li>b</li>
                     <li>c</li>
                     <li>d</li>
                 </ul>
             </div>
         </body>
     </html>
     ```

     我们可以通过Lxml库读取HTML文件的内容了，代码如下：

     ```python
     from lxml import etree
     
     html= etree.parse("html_demo.html")
     result = etree.tostring(html) 
     print(result)
     ```

     ![1567857170021](https://github.com/ai-union/PythonSpyder/blob/master/img/1567857170021.png?raw=true)

   - 解析HTML文件

     我们可以配合requests库获取HTML文件，然后利用Lxml库进行解析。

     网站为：https://www.qiushibaike.com/text/

     ```python
     import requests
     from lxml import etree
     
     header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)\
          AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36'}
     res = requests.get('https://www.qiushibaike.com/text/', headers = header)
     html = etree.HTML(res.text)
     result = etree.tostring(html)
     print(result)
     ```

     ![1567857830629](https://github.com/ai-union/PythonSpyder/blob/master/img/1567857830629.png?raw=true)

## Xpath的用法

1. 节点选择

   - 节点选择

     | 表达式   | 描述                                                     |
     | -------- | -------------------------------------------------------- |
     | nodename | 选取此节点的所有子节点                                   |
     | /        | 从根节点选取                                             |
     | //       | 从匹配选择的当前节点选择文档中的节点，而不考虑他们的位置 |
     | .        | 选取当前节点                                             |
     | ..       | 选取当前节点的父节点                                     |
     | @        | 选取属性                                                 |

   - 谓语

     | 路径表达式              | 结果                                                    |
     | ----------------------- | ------------------------------------------------------- |
     | /ul/li[1]               | 选取ul标签的子元素的第一个li元素                        |
     | //li[@attribute]        | 选取所有拥有名为attribute属性的li元素                   |
     | //li[@attribute='info'] | 选取所有的li元素，且这些元素拥有值为info的attribute属性 |

     我们手写这些内容可能会出错，所以通常我们式在谷歌浏览器的开发者模式下，通过Copy Xpath命令来获取。

   例如我们获取糗事百科的用户名：

   ```python
   import requests
   from lxml import etree
   
   header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)\
        AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36'}
   res = requests.get('https://www.qiushibaike.com/text/', headers = header)
   html = etree.HTML(res.text)
   user_id = html.xpath('//*[@id="qiushi_tag_122217427"]/div[1]/a[2]/h2/text()') # text()为获取内容的方法
   print(user_id)
   ```

   ![1567859645775](https://github.com/ai-union/PythonSpyder/blob/master/img/1567859645775.png?raw=true)

   > 这里有一点需要注意:我们注意到id属性中有一串数字，大家在写的时候，要根据自己的情况填写。否则获取不到数据。

   下面我们看一下如何获取当前页面中所有用户的名字：

   ```python
   import requests
   from lxml import etree
   
   header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)\
        AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36'}
   res = requests.get('https://www.qiushibaike.com/text/', headers = header)
   html = etree.HTML(res.text)
   
   users = html.xpath('//div[@class="article block untagged mb15 typs_hot"]')
   for user_info in users:
       user_id = user_info.xpath('div[1]/a[2]/h2/text()')[0]
       print(user_id)
   
   ```

   ![1567860876635](https://github.com/ai-union/PythonSpyder/blob/master/img/1567860876635.png?raw=true)

   但是如果们观看页面的话，会发现第一个的用户名并没有取到，因为第一个的class为“article block untagged mb15 typs_long”

   所以我们把代码改为以下：

   ```python
   import requests
   from lxml import etree
   
   header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)\
        AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36'}
   res = requests.get('https://www.qiushibaike.com/text/', headers = header)
   html = etree.HTML(res.text)
   
   users = html.xpath('//div[starts-with(@id,"qiushi_tag")]')
   for user_info in users:
       user_id = user_info.xpath('div[1]/a[2]/h2/text()')[0]
       print(user_id)
   
   ```

   ![1567861127369](https://github.com/ai-union/PythonSpyder/blob/master/img/1567861127369.png?raw=true)

   这里使用了一个**starts-with()** 它可以只匹配目标元素属性的一部分。

   好了，这便是本周的内容，大家可以用Xpath把每一个段子的内容获取一下，如果出现问题，可以加入我们的学习群获取帮助。