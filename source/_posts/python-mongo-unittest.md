title: Python Mongodb Unittest
date: 2014-07-16
categories:
- Python
tags:
- Python
- MongoDB
---

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/python-mongo-unittest.md
 

好久没更新博客了，今天马马虎虎充个数。
关于单元测试中如何处理mongodb的连接，网上众说纷纭。无外乎两种声音：1. 直接连接真实的mongodb数据库；2. 使用mock的方法。
个人感觉，直接连接真实数据库限制太大，所谓单元测试无非就是验证一个小小的单元小小的函数功能有没有满足预期的要求，连接真实数据库有很多问题，在开发机器上还好，有可用的mongodb数据库让你连，如果在CI服务器上跑测试，就很麻烦，每次build都要启动mongodb，而且要初始化数据库，测试完成要清理数据，甚至每跑一个test case都要在setup里初始化，teardown里clear数据库里的记录，如果有成百上千个test case，更加不便。
使用mock的方法就非常好，建议用这个https://github.com/vmalloc/mongomock ， 本人没有对这个库做太多复杂的mongodb查询操作（基于pymongo），功能的完整性尚且不了解。readme里有一个例子，很容易上手。

单元测试通过以后，整个系统做集成测试或者功能测试，就需要连真实的mongodb了，整个系统要run起来看效果，就相当于部署了，目前docker比较火，而且有官方的mongodb container，要起一个部署环境会非常快，测试完删除掉，每次都是新的，保证互不影响。目前还没有实践过，后面可以研究研究，一个基于mongodb的系统，从单元测试到整体部署做功能测试，在jenkins上如何实现