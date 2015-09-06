title: Django应用程序的性能云监控
date: 2015-09-06 13:34
categories:
- Python
tags:
- Python
- test
---


Django作为Python语言中最为流行的Web框架，受到越来越多的开发者欢迎。互联网上基于Django的站点越来越多。如何去监控Django应用是目前大家普遍关心的一个问题，Django应用经常会用到像Mongodb这样的NOSQL数据库来做后台存储，以及Memcached作缓存。本文将会试着搭建一个完整的Django应用并尝试去监控其性能。

# Django

## 介绍

Django（维基百科） Django是一个开放源代码的Web应用框架，由Python写成。采用了MVC的软件设计模式，即模型M，视图V和控制器C。它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站的。并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的。

Django的主要目标是使得开发复杂的、数据库驱动的网站变得简单。Django注重组件的重用性和“可插拔性”，敏捷开发和DRY法则（Don't Repeat Yourself）。在Django中Python被普遍使用，甚至包括配置文件和数据模型。

## 学习

Django的入门非常简单，本质上来说，Django 只不过是用Python编写的一组类库。 用Django开发站点就是使用这些类库编写Python代码。 因此，学习Django的关键就是学习如何进行Python编程并理解Django类库的运作方式。

如果你有Python开发经验，在学习过程中应该不会有任何问题。按照Django官方提供的教程一步步来，很快就可以简单入门，并且开发简单的Django应用。

# 搭建Django应用

如何搭建一个简单的Django应用。

## 安装

任何时候安装Python类库，都推荐使用[`virtualenv`](https://virtualenv.pypa.io/en/latest/)。然后可以通过源码或者`pip`安装。

## 创建一个简单的项目

```
django-admin.py startproject mysite
```
startproject 命令创建一个目录，包含4个文件：

```
mysite/
    __init__.py
    manage.py
    settings.py
    urls.py
```

如果你还没启动服务器的话，请切换到你的项目目录里 (cd mysite )，运行下面的命令：

```
python manage.py runserver
```
你会看到些像这样的
```
Validating models...
0 errors found.

Django version 1.0, using settings 'mysite.settings'
Development server is running at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
这将会在端口8000启动一个本地服务器, 并且只能从你的这台电脑连接和访问。 既然服务器已经运行起来了，现在用网页浏览器访问 http://127.0.0.1:8000/ 。 你应该可以看到一个令人赏心悦目的淡蓝色Django欢迎页面。 它开始工作了。

## MongoDB和Memcached

MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bjson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。
它的特点是高性能、易部署、易使用，存储数据非常方便。主要功能特性有：
    * 面向集合存储，易存储对象类型的数据。
    * 模式自由。
    * 支持动态查询。
    * 支持完全索引，包含内部对象。
    * 支持查询。
    * 支持复制和故障恢复。
    * 使用高效的二进制数据存储，包括大型对象（如视频等）。
    * 自动处理碎片，以支持云计算层次的扩展性
    * 支持RUBY，PYTHON，JAVA，C++，PHP等多种语言。
    * 文件存储格式为BSON（一种JSON的扩展）
    * 可通过网络访问

MongoDB有基于Python的API——Pymongo。Pymongo用于操作MongoDB非常简单。例如：

```
# Connection
from pymongo import MongoClient
client = MongoClient('mongodb://localhost:27017/')
db = client.test_database 

# Insert
db.posts.insert({"a": "b"})  
```

memcached是一种缓存技术，存储在内存中(高性能分布式内存缓存服务器)。目的：提速。（传统的都是把数据保存在关系型数据库管理系统即RDBMS，客户端请求时会从RDBMS中读取数据并在浏览器中显示，这样当访问量过大时或集中时，导致RSBMS负担过重，数据库响应恶化，浏览器中显示延迟等严重问题，使用memcached减少数据库查询和访问次数以提高访问速度，提高扩展性）

Python对于Memcached的操作也非常简单:

```
import memcache

mc=memcache.Client(['127.0.0.1:11211'],debug=0)
mc.set(“some_key”,”Some value”)
value=mc.get(“some_key”)
mc.set(“another_key”,3)
mc.delete(“another_key)
mc.set(“key”,”1″) #用于自动增量/减量的必须是字符串
mc.incr(“key”)
mc.decr(“key”)

```

有了这些基本操作，就可以学习在Django应用中使用MongoDB和Memcached了。

# 使用听云Python探针监控Django APP

打开https://report.tingyun.com/overview/application新建一个应用。

## 下载安装探针

![](https://static.oschina.net/uploads/img/201509/06210247_47hG.png)

```
# 安装
$ pip install tingyun

#生成配置
$ tingyun-admin generate-config YourLicenseKey /tmp/tingyun.ini

# 设置环境变量
$ export TING_YUN_CONFIG_FILE=/tmp/tingyun.ini

```

## 启动探针

```
# 重启应用
$ tingyun-admin run-program 您应用的启动命令 您应用启动参数
```
```
# 查看log
$ tail -f /tmp/tingyun-agent.log 
2015-09-06 19:43:38,531 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.http.multipartparser' from '/usr/local/lib/python2.7/dist-packages/django/http/multipartparser.pyc'>
2015-09-06 19:43:38,551 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.template.base' from '/usr/local/lib/python2.7/dist-packages/django/template/base.pyc'>
2015-09-06 19:43:38,560 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.core.urlresolvers' from '/usr/local/lib/python2.7/dist-packages/django/core/urlresolvers.pyc'>
2015-09-06 19:43:38,561 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.core.handlers.base' from '/usr/local/lib/python2.7/dist-packages/django/core/handlers/base.pyc'>
2015-09-06 19:43:38,562 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.core.handlers.wsgi' from '/usr/local/lib/python2.7/dist-packages/django/core/handlers/wsgi.pyc'>
2015-09-06 19:43:38,564 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.framework_django for target module <module 'django.template.loader_tags' from '/usr/local/lib/python2.7/dist-packages/django/template/loader_tags.pyc'>
2015-09-06 19:43:38,618 (14365/MainThread) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.database_dbapi2 for target module <module 'MySQLdb' from '/usr/local/lib/python2.7/dist-packages/MySQLdb/__init__.pyc'>
2015-09-06 19:43:38,669 (14365/Dummy-1) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.database_mongo for target module <module 'pymongo.collection' from '/usr/local/lib/python2.7/dist-packages/pymongo/collection.pyc'>
2015-09-06 19:43:38,671 (14365/Dummy-1) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.database_mongo for target module <module 'pymongo.mongo_client' from '/usr/local/lib/python2.7/dist-packages/pymongo/mongo_client.pyc'>
2015-09-06 19:43:38,671 (14365/Dummy-1) tingyun.embattle.inspection 144 INFO - Detect hooker tingyun.armoury.database_mongo for target module <module 'pymongo.connection' from '/usr/local/lib/python2.7/dist-packages/pymongo/connection.pyc'>
```

## 查看监控结果

探针会尝试与听云服务器建立连接，然后上传监控数据。

整个应用的拓扑：
![topo](https://static.oschina.net/uploads/img/201509/06211402_KPR5.png "整个应用拓扑")

一些web监控数据：

![输入图片说明](https://static.oschina.net/uploads/img/201509/06211521_FKlF.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06211729_nuA5.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06211902_Quf2.png "在这里输入图片标题")

一些数据库的监控数据

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212201_WRAi.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212220_SFMe.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212241_Idxv.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212405_vP12.png "在这里输入图片标题")

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212425_GQaw.png "在这里输入图片标题")

对于服务器资源的监控

![输入图片说明](https://static.oschina.net/uploads/img/201509/06212710_omgU.png "在这里输入图片标题")

# 总结

总体来说，虽然处于测试阶段，听云的这种基于云的应用监控服务做的还是不错的，所提供的数据对于我们了解自己的应用的运行情况起到了很大得帮助。笔者并没有全部展现其功能，更多的细节有待日后挖掘，感兴趣的童鞋可以阅读其Python探针的源码，来了解其背后的原理和过程。

# 参考资料：

- [1]: https://coderfactory.com/posts/top-sites-built-with-django
- [2]: http://docs.30c.org/djangobook2/index.html
- [3]: http://www.runoob.com/mongodb/mongodb-intro.html
- [4]: https://api.mongodb.org/python/current/
- [4]: http://blog.csdn.net/guugle2010/article/details/40115675
- [5]: http://www.tingyun.com/