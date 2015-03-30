title: OpenStack: oslo.config
date: 2014-10-19 22:20:57
categories:
- OpenStack
tags:
- OpenStack
- Python
---

![](/thumbnails/oslo-config/openstack.png)


#### 参考资料

[https://wiki.openstack.org/wiki/Oslo](https://wiki.openstack.org/wiki/Oslo)

[https://wiki.openstack.org/wiki/Oslo/Config](https://wiki.openstack.org/wiki/Oslo/Config)

[Openstack Oslo.config 学习(一)](http://www.choudan.net/2013/11/27/OpenStack-Oslo.config-%E5%AD%A6%E4%B9%A0%28%E4%B8%80%29.html)

[Openstack Oslo.config 学习(二)](http://www.choudan.net/2013/11/28/OpenStack-Oslo.config-%E5%AD%A6%E4%B9%A0(%E4%BA%8C).html)

[OpenStack源码探秘（二）——Oslo.config](http://blog.csdn.net/networm3/article/details/8946556)


简单试用了一下，感觉这个openstack的库还是挺不错的，可以用于自己的项目中，进行配置管理。

之前比较常用的就是`ConfigParser`和`argparse`这两个库进行命令行和配置文件的配置管理。`oslo.confi`g感觉是通过一个库做了统一。

```python
""" test.py"""
import sys
from oslo.config import cfg
CONF = cfg.CONF
CONF(args=sys.argv[1:])
```
上面四行代码可以运行：
```bash
$ python test.py -h
usage: test.py [-h] [--config-dir DIR] [--config-file PATH] [--version]

optional arguments:
  -h, --help          show this help message and exit
  --config-dir DIR    Path to a config directory to pull *.conf files from.
                      This file set is sorted, so as to provide a predictable
                      parse order if individual options are over-ridden. The
                      set is parsed after the file(s) specified via previous
                      --config-file, arguments hence over-ridden options in
                      the directory take precedence.
  --config-file PATH  Path to a config file to use. Multiple config files can
                      be specified, with values in later files taking
                      precedence. The default files used are: None.
  --version           show program's version number and exit
```

可以看到，有两个重要的配置参数：`config-dir`和`config-file`。

下面我们准备一个config文件`test.conf`
``` bash
$ more test.conf

[DEFAULT]
bind_host = 192.168.0.1
bind_port = 55553
```
假如在主程序中要获取到这个配置，该怎么办？

在CONF这个全局变量里

```python
print 'host:', CONF.bind_host
print 'port:',CONF.bind_port
```
运行 `python test.py --config-file=test.conf` 会报错：

``` python
Traceback (most recent call last):
  File "test.py", line 24, in <module>
    print CONF.bind_host
  File "/home/demo/virtulenv/openstack/local/lib/python2.7/site-packages/oslo/config/cfg.py", line 1697, in __getattr__
    raise NoSuchOptError(name)
oslo.config.cfg.NoSuchOptError: no such option: bind_host
```
没有这个option，原因时我们没有注册。

注册一下：
``` python
opts = [
    cfg.StrOpt('bind_host', default='0.0.0.0', help='IP address to listen on'),
    cfg.IntOpt('bind_port', default=9292, help='Port number to listen on'),
]

CONF.register_opts(opts)
```
在config的ini文件中可以分group，上面就是定义了默认group里的配置，并且给了默认值，假如我们如下面这样运行
``` bash
$ python test.py       
host:  0.0.0.0
port: 9292
```
即使没有test.conf文件，这两个配置选项也有值，就是默认值。加入加上`--config-file`。
``` bash
$ python test.py --config-file=test.conf
host:  192.168.0.1
port: 55553
```
发现配置项的值被test.conf里的值覆盖了。在实际使用中，一般test.conf会这么写：

``` bash
$ more test.conf

[DEFAULT]
#bind_host = 0.0.0.0
#bind_port = 9292
```
初始化是默认参数，是注释掉的，只有用户实际环境需要改的时候才会修改配置文件，然后运行时加载，配置文件里的配置生效，取代默认值。因为可以有多个配置文件，如果同一项配置在多个文件里出现，最有一个文件里的配置生效。例如，有`test2.conf`这个文件里也定义了`bind_host`，如果test.conf和test2.conf里的值都没有注释，并且运行：
``` bash
$ python test.py --config-file=test.conf --config-file=test2.conf 
```
`bind_host`值讲采用第二个配置文件里的值。

有时候为了更方便的更改配置参数，我们想让参数可以在命令行直接配置，怎么做呢？ 很简单，注册的时候注册到cli即可

``` python
opts = [
    cfg.StrOpt('bind_host', default='0.0.0.0', help='IP address to listen on'),
    cfg.IntOpt('bind_port', default=9292, help='Port number to listen on'),
]

CONF.register_cli_opts(opts)
```

此时命令行：
```
$ python test.py -h
usage: test.py [-h] [--bind_port BIND_PORT] [--config-dir DIR]
               [--config-file PATH] [--version] [--bind_host BIND_HOST]

optional arguments:
  -h, --help            show this help message and exit
  --bind_port BIND_PORT
                        Port number to listen on
  --config-dir DIR      Path to a config directory to pull *.conf files from.
                        This file set is sorted, so as to provide a
                        predictable parse order if individual options are
                        over-ridden. The set is parsed after the file(s)
                        specified via previous --config-file, arguments hence
                        over-ridden options in the directory take precedence.
  --config-file PATH    Path to a config file to use. Multiple config files
                        can be specified, with values in later files taking
                        precedence. The default files used are: None.
  --version             show program's version number and exit
  --bind_host BIND_HOST
                        IP address to listen on
```

运行
```
$ python test.py --bind_port=1234 
host: 0.0.0.0
port: 1234
```

同样命令行的配置参数可以覆盖前面配置文件的参数

```
$ python test.py --config-file=test.conf --bind_port=1234 
host: 0.0.0.0
port: 1234
```

如何给命令行添加更多参数，以及如何在项目中使用CONF全局变量，更详细的内容请参考参考资料。

