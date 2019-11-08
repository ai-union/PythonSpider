# 专栏第八周 Scrapy爬虫开发

到目前为止我们基本掌握了基础的爬虫开发，但是基本都是比较简单的网站爬虫。我们有没有什么比较快速方便的构建完整的解决方案呢？本周就为大家介绍一个非常流行的Scrapy爬虫框架。

## 认识与安装Scrapy

爬虫框架能为项目开发起到规范的作用，也因如此使其失去了一定的灵活性。很多人会将Requests和Scrapy两者进行对比，前者是第三方库，后者是爬虫开发框架，尽管两者不在同一层次上，但还是有一定的对比性。

- 规范性：Scrapy自带功能模块能完成爬虫开发，各个功能代码划分明确。Requests只规范数据爬取，不支持数据清洗和数据存储，需结合其他库一起使用才能完成爬虫开发。
- 灵活性：Scrapy的灵活性不如Requests,对于一些设计不合理的网站或者特殊的网站，Requests更适合一些。
- 适用范围：Scrapy适合大型爬虫开发项目，便于开发者对代码的维护和管理。Requests受开发者的习惯影响较大，如果架构设计不合理或者更换开发人员，会使代码难以维护和管理。

1. Scrapy的运行机制

   Scrapy使用Twisted异步网络库来处理网络通信，架构清晰，并且包含各个中间件接口，可以灵活的完成各种需求。Scrapy的整体架构图如下：

   ![Scrapy](https://github.com/ai-union/PythonSpyder/blob/master/img/Scrapy.jpg?raw=true)

   Scrapy的运行机制大概如下：

   - 引擎从调度器中取出一个URL，用于接下来的抓取。
   - 引擎把URL封装成请求传给下载器，下载器把资源下载后封装成应答包。
   - 爬虫解析Response。
   - 若解析出实体（Item），则交给实体管道进行进一步的处理。
   - 若解析出的是URL，则把URL交给Scheduler等待抓取。

   结合上图，各个组件的功能说明如下：

   - 引擎（Scrapy Engine）:处理整个系统的数据流，触发事务（框架核心）。
   - 调度器（Scheduler）:接受引擎发过来的请求，压入队列中，并在引擎再次请求的时候返回。
   - 下载器（Downloader）:用于下载网页内容，并将网页内容返回给爬虫（Spiders）。
   - 爬虫（Spiders）：从特定的网页中提取自己需要的信息，即实体（Item）。也可以从中提取URL，让Scrapy继续抓取下一个页面。
   - 项目管道（Item Pipeline）：负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体，验证实体的有效性，清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。
   - 下载器中间件（Downloader Middlewares）：位于Scrapy引擎和下载器之间的框架，处理引擎与下载器之间的请求及响应。
   - 爬虫中间件（Spider Middlewares）:介于Scrapy引擎和爬虫之间的框架，主要工作是处理爬虫的响应输入和请求输出。
   - 调度中间件（Scheduler Middlewares）:介于Scrapy引擎和调度器之间的中间件，用于处理Scrapy引擎发送到调度器的请求和响应。

2. 安装Scrapy

   在安装Scrapy之前，需要先安装Twisted。可以使用pip安装，但容易出问题，建议下载whl文件安装。

   在安装完Twisted后可以使用pip安装Scrapy：

   ```shell
   pip install Scrapy
   ```

   ![1569076766706](https://github.com/ai-union/PythonSpyder/blob/master/img/1569076766706.png?raw=true)

   这样我们的安装就完成了。

## Scrapy爬虫开发示例

我们通过爬取百度知道https://zhidao.baidu.com/list?cid=110106演示如何使用Scrapy获取问题列表。

分析页面得知每条数据在\<a>标签中，而\<a>标签则在class属性为question-title的\<div>标签中。

![1569077409152](https://github.com/ai-union/PythonSpyder/blob/master/img/1569077409152.png?raw=true)

1. 我们切换到工作目录下，然后通过命令创建一个叫做baidu的项目。

   ```python
   scrapy startproject baidu
   ```

   ![1569077656059](https://github.com/ai-union/PythonSpyder/blob/master/img/1569077656059.png?raw=true)

   我们会看到自动创建好的文件夹目录

   ![1569077783474](https://github.com/ai-union/PythonSpyder/blob/master/img/1569077783474.png?raw=true)

   - spiders(文件夹):编写爬虫规则，实现数据爬取和数据清洗处理。
   - items.py:数据定义和实例化，用于寄存清洗后的数据。
   - middlewares.py:是介于Scrapy的request/response处理的钩子框架，用于全局修改Scrapy request和response的一个轻量，底层的系统。
   - pipelines.py:执行保存数据的操作，数据对象来源于items.py。
   - setting.py:整个框架配置文件。
   - scrapy.cfg:项目部署文件。

   Scrapy的常用开发顺序为：

   - setting.py：主要配置爬虫，比如请求头什么的。
   - items.py：定义存储数据对象，主要衔接spiders和pipelines.py。
   - pipelines.py：数据存储，数据格式为字典，key为items.py定义的变量。
   - spiders：编写爬虫规则。

   2.开始编写爬虫

   - 修改setting.py：初始文件大部分内容都是注释掉的。本项目只需要设置Item Pipeline和请求头即可。修改如下：

     ```python
     pass # 上面的内容省略
     # Override the default request headers:
     DEFAULT_REQUEST_HEADERS = {
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
        'Accept-Language': 'en',
     }
     pass # 省略
     # Configure item pipelines
     # See https://docs.scrapy.org/en/latest/topics/item-pipeline.html
     ITEM_PIPELINES = {
         'baidu.pipelines.BaiduPipeline': 300,
     }
     ```

     > 由于篇幅问题，省略了其他代码

   - 修改items.py文件

     ```python
     import scrapy
     
     
     class BaiduItem(scrapy.Item):
         # define the fields for your item here like:
         # name = scrapy.Field()
         TitleName = scrapy.Field()
         pass
     ```

   - 修改pipelines.py。我们将爬取的内容保存在txt中。

     ```python
     class BaiduPipeline(object):
         def process_item(self, item, spider):
             with open('data.txt', 'a') as f:
                 for i in item['TitleName']:
                     value = i.replace("\n","")
                     f.write(value + "\r\n")
             return item
     ```

   - 在spiders文件夹下面创建爬虫规则。Spider_spiders.py，代码如下：

     ```python
     # 导入items.py的BaiduItem，存放爬取数据
     from baidu.items import BaiduItem
     # Scrapy自带的数据清洗模块
     from scrapy.selector import Selector
     # Scrapy搜素引擎
     from scrapy.spider import Spider
     # 爬虫规则
     class Baiduspider(Spider):
         # 属性name必须设置,且唯一,用于运行爬虫
         name = "Baidu_know"
         # 设置允许访问域名
         allowed_domins = ['baidu.com']
         # 设置URL
         start_urls = [https://zhidao.baidu.com/list?cid=110106","https://zhidao.baidu.com/list?cid=110106"]
         # 函数parse处理响应内容，函数名不能更改
         def parse(self, response):
             # 将响应内容生成Selector用于数据清洗
             sel = Selector(response)
             items = []
             item = BaiduItem()
             title = sel.xpath('//div[@class="question-title"]/a/text()').extract()
             for i in title:
                 items.append(i)
             item['TitleName'] = items
             return item
     ```

   - 在终端中允许如下代码：

     ```shell
     scrapy crawl Baidu_know
     ```

     ![1569084529057](https://github.com/ai-union/PythonSpyder/blob/master/img/1569084529057.png?raw=true)

     我们可以看到，两个网站都进行了爬取，只是没有获取到数据。这样我们的第一个Scrapy项目就算允许成功了。关于Scrapy的详细内容大家还是要多多的看文档和书。欢迎大家进群讨论。

