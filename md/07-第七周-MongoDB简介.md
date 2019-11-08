# 专栏第七周 MongoDB简介

MongoDB是一个基于分布式文件存储的数据库，由C++语言编写，旨在为web应用提供可扩展的高性能数据存储解决方案。由于可扩展性，因此非常适合爬虫数据的存储。

> 在本篇文章中，你讲学会一下内容：
>
> - MongoDB的安装
> - MongoDB在Python中的基本用法
> - MongoDB在爬虫中的使用

## MongoDB的下载和安装

1. 下载地址：https://www.mongodb.com/download-center/community

   进入官网后我们选择Server，然后根据自己的电脑系统选择合适的版本

   ![1568437445627](https://github.com/ai-union/PythonSpyder/blob/master/img/1568437445627.png?raw=true)

   > **注意：在 MongoDB 2.2 版本后已经不再支持 Windows XP 系统。最新版本也已经没有了 32 位系统的安装文件。**

2. 下载好文件，我们双击运行。

   ![1568438083620](https://github.com/ai-union/PythonSpyder/blob/master/img/1568438083620.png?raw=true)

   ![1568438111443](https://github.com/ai-union/PythonSpyder/blob/master/img/1568438111443.png?raw=true)

   > 这边的路径我建议不要保存到有中文或者符号的文件夹

   然后点下一步，其他的我都选择了默认的。

   ![1568438387703](https://github.com/ai-union/PythonSpyder/blob/master/img/1568438387703.png?raw=true)

   ![1568438437191](https://github.com/ai-union/PythonSpyder/blob/master/img/1568438437191.png?raw=true)

   ![1568438455277](https://github.com/ai-union/PythonSpyder/blob/master/img/1568438455277.png?raw=true)

   > 注意：在最后一步的时候，它会问你是否关闭XXX应用，我这里选择否。
>
   > ※**最后一步，一直卡着不动**
>
   > 是因为到最后一步时，左下角的勾勾没有去掉，mongodb compass是图形化管理界面，下载它需要很久很久，还有可能一直下不来，所以把勾去掉就能马上安装好。

3. 配置与启动

   在启动前，我们需要在一个目录下面创建一个用来保存数据的文件夹。如：D:\mongoDB\data

   然后切换到MongoDB的安装目录下的bin目录

   ```shell
   cd /d D:\Program Files\MongoDB\Server\4.2\bin
   ```

   然后执行以下命令来启动MongoDB

   ```shell
   mongod --dbpath D:\mongoDB\data
   ```

   D:\mongoDB\data 为你刚刚创建好的文件夹路径，记得要有权限，或者会报错。

   ![1568442757901](https://github.com/ai-union/PythonSpyder/blob/master/img/1568442757901.png?raw=true)

4. 然后我们新开一个窗口

   同样是进入mongoDB的bin目录

   ```shell
   cd /d D:\Program Files\MongoDB\Server\4.2\bin
   ```

   ![1568442863312](https://github.com/ai-union/PythonSpyder/blob/master/img/1568442863312.png?raw=true)

   然后输入：mongo

   ![1568442901842](https://github.com/ai-union/PythonSpyder/blob/master/img/1568442901842.png?raw=true)

   看到 > 就表示启动成功了。

## 在Python中使用MongoDB

1. 安装pymongo库

   ```python
   pip install pymongo
   ```

   如果安装失败，可以尝试以下命令

   ```python
   pip install pymongo -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
   ```

   ![1568443442203](https://github.com/ai-union/PythonSpyder/blob/master/img/1568443442203.png?raw=true)

   如上图表示安装成功。

2. 导入pymongo库

   ```python
   import pymongo # 执行此命令前要保证mongoDB已经启动成功
   ```

3. 建立连接

   ```python
   client = pymongo.MongoClient('localhost', 27017)
   ```

4. 新建库

   ```python
   db = clinet['db_name']
   ```

5. 新建表

   ```python
   table = db['table_name']
   ```

6. 写入数据

   ```python
   table.insert({'key1':value1, 'key2':value2})
   ```

7. 删除数据

   ```python
   table.remove({'key':value})
   ```

8. 修改数据

   ```python
   table.update({'key':value},{"$set":{'key1':value1, 'key2':value2}})
   ```

9. 查询数据

   ```python
   table.find({'key':value})
   ```

##  MongoDB在爬虫中的使用

我们这里爬取的是：https://where.heweather.com/location.html这个网站的热门城市代码

![1568447114208](https://github.com/ai-union/PythonSpyder/blob/master/img/1568447114208.png?raw=true)

![1568447140842](https://github.com/ai-union/PythonSpyder/blob/master/img/1568447140842.png?raw=true)

我们讲爬取的信息保存进MongoDB

具体代码如下：

```python
import requests
import pymongo # 导入pymongo


header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)\
     AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.75 Safari/537.36'}

req = requests.get('https://search.heweather.net/top?group=cn&lang=zh&key=b44262251121469585bc2d212d33a3b3', headers=header)
client = pymongo.MongoClient('localhost', 27017)
db = client['cityCode'] # 创建cityCode这个库
table = db['code_list'] # code_list 这个表
table.insert_one(req.json())
```

运行效果：

![1568447241296](https://github.com/ai-union/PythonSpyder/blob/master/img/1568447241296.png?raw=true)

这个管理工具是：

![1568447268341](https://github.com/ai-union/PythonSpyder/blob/master/img/1568447268341.png?raw=true)

下载地址：https://robomongo.org/download

好了，本周的内容就是这些，MongoDB的用法还有很多，这里只是简单的介绍以下，如果要深入学习的话，建议买一本书来看看，或者加群获取电子资料。











