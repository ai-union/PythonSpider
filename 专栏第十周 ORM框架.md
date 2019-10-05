# 专栏第十周 ORM框架

我们的爬虫专栏基本到这里就算是完结了，接下来的几篇文章会介绍一些我们在项目中常用的第三方库或者是框架，有这些框架的支持我们在编写爬虫项目时，会方便很多。今天为大家介绍的这个ORM框架就是用来操作数据库的。

## SQLAlchemy框架介绍与安装

SQLAlchemy是Python编程语言下的一款开源软件，提供SQL工具包以及对象-关系映射工具，使用MIT许可证发行。它的一个目标是提供能兼容众多数据库（如SQLite，MySql，Postgres，Oracle，MS-SQL，SQLServer和Firebird）的企业级持久性模型。

1. SQLAlchemy的安装

   我们通过如下命令安装：

   ```python
   pip install SQLAlchemy
   ```

   如果安装比较慢或者失败可以用试试更换一下源，如下：

   ```python
   pip install -i https://pypi.tuna.tsinghua.edu.cn/simple SQLAlchemy
   ```

   ![1570202323349](https://github.com/ai-union/PythonSpyder/blob/master/img/1570202323349.png?raw=true)

   使用SQLAlchemy连接数据库实质上还是通过数据库接口实现连接，安装SQLAlchemy后还需要安装对应数据库的接口模块，下面以MySql为例安装pymysql：

   ```python
   pip install pymysql
   ```

   ![1570202356214](https://github.com/ai-union/PythonSpyder/blob/master/img/1570202356214.png?raw=true)

   安装完成后我们在命令行窗口中导入模块来测试是否安装成功：

   ![1570202509673](https://github.com/ai-union/PythonSpyder/blob/master/img/1570202509673.png?raw=true)

2. SQLAlchemy在Python中操作数据库

   SQLAlchemy连接数据库使用数据库链接池技术，原理是在系统初始化的时候，将数据库连接作为对象存储在内存中，当用户需要访问数据库时，并非建立一个新的连接，而是从连接池中取出一个已建立的空闲连接对象。使用完毕后，用户也并非将连接关闭，而是将连接放回连接池中，以供下一个请求访问使用。而连接的建立，断开都是由连接池自身来管理。同时，还可以通过设置连接池参数来控制连接池中的初始连接数，连接的上下限数以及每个连接的最大使用次数，最大空闲时间等。也可以通过其自身的管理机制来监视数据库连接的数量以及使用情况等。

   说完原理后，我没来看看SQLAlchemy连接数据库的代码：

   ```python
   from sqlalchemy import create_engine
   engine = create_engine("mysql+pymysql://root:pwd@localhost:3306/test?charset=utf8",echo=True)
   ```

   - mysql+pymysql://root:pwd@localhost:3306/test： mysql指明数据库系统类型，pymysql是连接数据库接口的模块，root是数据库用户名，pwd是数据库密码，localhost:3306是本地数据库和端口号,test是数据库名。
- echo=True：用于显示SQLAlchemy在操作数据库时所执行的SQL语句情况，相当于一个监视器，可以清楚知道执行情况。
   
   其他数据库的连接：
   
   | 数据库              | 连接字符串                                  |
   | ------------------- | ------------------------------------------- |
   | Microsoft SQL Sever | mssql+pymssql://usename:pwd@ip:port/dbname  |
   | MySql               | mysql+pymysql://username:pwd@ip:port/dbname |
   | Oracle              | cx_Oracle://username:pwd@ip:port/dbname     |
   | PostgreSQL          | postgresql://username:pwd@ip:port/dbname    |
   | SQLite              | sqlite://file_path                          |

## 创建数据库表

我们在连接好数据库后，接下来就是创建数据库表了，接下来为大家演示如何用SQLAlchemy创建表：

```python
# 连接数据库 
from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://root:root@localhost:3306/test?charset=utf8",echo=True)

# 创建数据库
from sqlalchemy import Column, Integer, String, DateTime # 常用数据类型
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

class mytable(Base):
    # 表名
    __tablename__ = "mytable"
    # 字段，属性
    id = Column(Integer, primary_key = True)
    name = Column(String(10), unique = True)
    age = Column(Integer)
    brith = Column(DateTime)
    class_name = Column(String(50))

Base.metadata.create_all(engine)
```

\__tablename__ = "mytable"可以省略，如果省略那么表名和类名就是一样的。类中的属性就是数据中的字段名以及类型。最后通过Base.metadata.create_all(engine)在数据库中创建对应的数据库表。

如果要删除数据表可以通过如下代码：

```python
# 先删除类，后删除数据库表
mytable.drop(bind = engine)
Base.metadata.drop_all(engine)
```

无论是创建还是删除都需要先对数据进行连接，即创建engine对象。

## 添加数据

完成数据表的创建后，接下来我们就可以进行数据的操作。首先创建一个会话对象，用于执行SQL语句，代码如下：

```python
from sqlalchemy.orm import sessionmaker
DBSession = sessionmaker(bind = engine)
session = DBSession()
```

我们常用的增，删，改，查在SQLAlchemy中有固定的语法支持。

```python
new_data = mytable(name = 'Tango', age = 10, birth='2019-10-05', class_name = '三年一班')
session.add(new_data)
session.commit()
session.close()
```

## 更新数据

- 使用update方法更新数据

  ```python
  session.query(mytable).filter_by(id=1).update({mytable.age:12})
  session.commit()
  session.close()
  ```

  首先我们查找到id为1的数据，然后对这条数据的age进行修改。

- 使用赋值方式更新：

  ```python
  get_data = session.query(mytable).filter_by(id=1).first()
  get_data.class_name = '三年二班'
  session.commit()
  session.close()
  ```

  这种方式通常用于做单条数据的更新，如果要批量更新那么update的性能会更好一些。

## 查询数据

- 查询所有数据

  ```python
  get_data = session.query(mytable).all()
  for i in get_data:
      print('我的名字' + i.name)
      print('我的班接' + i.class_name)
  session.close()
  ```

  这种写法相当于sql中的：

  ```sql
  select * from mytable;
  ```

  all()的返回类型是列表。

- 查询指定字段

  ```python
  get_data = session.query(mytable.name, mytable.class_name).all()
  for i in get_data:
      print('我的名字' + i.name)
      print('我的班接' + i.class_name)
  session.close()
  ```

  我们只需要将要查的字段传入query中。

- 设置筛选条件

  ```python
  get_data = session.query(mytable.name, mytable.class_name).filter_by(id=1).all()
  for i in get_data:
      print('我的名字' + i.name)
      print('我的班接' + i.class_name)
  session.close()
  
  ```

  filter_by(id=1)指定的是查询对象id为1的那条数据。

  如果要获取查询结果中的第一天只需要将all()替换成first()就可以了。

  - and多条件查询

    ```python
    get_data = session.query(mytable.name, mytable.class_name).filter(mytable.id>2, mytable.name=='Tango').first()
    print('我的名字' + get_data.name)
    print('我的班接' + get_data.class_name)
    ```

  - or条件查询

    ```python
    from sqlalchemy import or_
    session.query(mytable).filter(or_(mytable.id>2, mytable.name=='Tango')).all()
    ```

    需要引入or_

  - 多表查询

    ```python
    get_data = session.query(mytable).join(mytable2).filter(mytable.id>=2).all()
    ```

    join为内连，如果需要使用外连把join换成outerjoin就好。

    如果执行的sql语句很复杂我们也可以直接调用原生的SQL语句

    ```python
    sql = "select * from mytable "
    session.excute(sql)
    session.commit()
    ```

好了，这周的内容就是这些了，大家可以将之前的爬虫获取的数据通过这个框架添加到数据库中看看效果如何。