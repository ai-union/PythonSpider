# Python基础

# Python环境安装

1. 官网地址：https://www.python.org/

2. Anaconda集成环境：https://www.anaconda.com/

   > 本专栏推荐使用这个环境

   **注意点**

   - 下载时，记得选择和自己系统对应的版本

     ![1564807382096](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564807382096.png)

   - 记得下载**3.7**版本。*本片文章发布时，最新版本是3.7*

   - 安装路径不要有中文或者特殊符号。

   - 安装过程中，你会看到有**两个复选框**，记得把他们**都选上**。

3. 安装成功后，打开你的命令行工具，然后输入**Python**看看能得到什么？

   ![1564807688948](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564807688948.png)

   

   > 我这里没有使用windows系统自带的CMD，而是下载了一个叫做"cmder"的软件

   如果你看到类似上图的界面，那么恭喜你，你已经学会了Python的50%。剩下的就是学习如何写代码了。

## Python基础知识

1. Python是用缩进（四个空格）来控制代码层级关系的。这点很重要。所以写在了最前面。

2. 基本数据类型

   - Number（数值）

     - int （有符号整数）。比如：10， -10， 0等
     - float（浮点型）。就是我们常说的小数。比如：0.5，3.14等
     - complex（复数）。由实数和虚数两部分构成，形式为a + bj。由于爬虫用到的比较少，这里只需要知道一下就好。

   - String（字符串）

     字符串是用来表示文本的数据类型。比如：name = "路飞"

     - 提取字符

       ```python
       name = "路飞"
       name[0] # 这个将返回‘路’
       ```

       ![1564808967916](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564808967916.png)

     - 字符串切片

       ```python
       word = "人生苦短，我用Python"
       # 需要将苦短提取出来
       word[2:4]
       ```

       ![1564809335298](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564809335298.png)

       > 注意切片是包含左边，不包含右边的数值。

       也可以从右边数

       ```python
       # 提取Python
       word[-6:]
       ```

       ![1564809595601](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564809595601.png)

     - 拼接， 直接用加号就可以将多个字符串拼接在一起

       ```python
       name = "路飞"
       word = "人生苦短，我用Python"
       name + word
       ```

       ![1564810175374](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564810175374.png)

     - 格式化字符串。有的时候我们的字符串中某个内容需要变动，这个时候就可以私用format()函数了。

       ```python
       name = "路飞"
       msg = "你好啊，{}".format(name)
       ```

       ![1564810438026](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564810438026.png)

       > 更多的用法，我们在实际代码中再为大家介绍。

   - List（列表）

     python中用 [ ] 来表示列表

     ```python
     name_list = ['路飞', '索隆', '娜美']
     ```

   - Dictionary（字典）

     字典是值包含由键和值组成的数据集合

     ```python
     info = {'name':'路飞', 'age':18}
     ```

     > 要注意的是键不可以重复，而值是可以的。

   - Tuple（元组）

     它和列表很相似，用（）来表示。但是它一旦船家女之后就不能修改了。

     ```python
     t = ('a', 'b')
     ```

3. 函数

   - 函数的定义方式

     ```python
     def getInfo():
         print("这是一个函数")
     ```

     > 我们可以通过def 加我们的函数名，来创建函数。

   - 函数的调用

     ```python
     def getInfo():
         print("这是一个函数")
     getInfo()
     ```

     ![1564812010365](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564812010365.png)

   - 全局变量

     ![1564812252811](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564812252811.png)

     函数内是可以调用全局变量的，但是如果要修改那么就要加上global关键字。

     ![1564812421392](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564812421392.png)

   - 参数

     函数是可以接收参数的

     ![1564812728360](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564812728360.png)

     这里的name就是参数，通常我们叫它“形参”

4. 条件判断与循环

   - 判断

     ![1564812936144](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564812936144.png)

     if 后面接的是表达式，接是否为真，为真的时候就会执行该语句的代码。如果为假的时候就会执行elif的判断或者else后面的判断。

   - 循环

     - for 循环

       我们前面知道Python中有列表，那么我如果想变量列表中的所有元素该怎么操作呢?

       ![1564813159414](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564813159414.png)

       我们看到，列表里面的名字依次被打印了出来

     - while循环

       我们想让程序从从1开始数，数到10停止。

       ![1564813334042](C:\Users\baohu\AppData\Roaming\Typora\typora-user-images\1564813334042.png)

       while后面接的也是表达式，当条件满足时就会执行。

       > 注意一定是一个可以结束的表达式，否则会进入死循环。这里的 i += 1，就是为了让i 每次都加1，这样当i 大于等于10的时候就不会再执行了。

       - 我们用break可以提前结束循环
       - 用continue可以跳过某次循环，具体用法我们再后面的爬虫项目中会和大家慢慢介绍。

5. 类

   这个概念比较抽象，简单的说就是用代码来描述一类事物。比如狗，猫，车这些。

   在python中我们可以用class关键字来定义一个类

   ```python
   class Car():
       '''
       这是一个车的类
       '''
   ```

## 最后

我们这里只是简单的介绍了一下在爬虫中要用到的Python技术。由于篇幅的原因未能给大家讲的很细，希望大家见谅。读者可以关注本公众号获取更多关于的Python基础的文章。

然我讲知识点放到实际爬虫项目中。欢迎大家跟着Tango继续后面的爬虫相关的内容。我们下周见。