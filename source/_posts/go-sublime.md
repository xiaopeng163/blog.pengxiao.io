title: 使用Sublime Text搭建Go开发环境
date: 2015-04-25
categories:
- Go
tags:
- Go
---

![](/thumbnails/install-go-from-source/1.png)

source https://github.com/xiaopeng163/www.pengxiao.me/blob/master/source/_posts/go-sublime.md

### Install package control

首先安装[Package Control](https://packagecontrol.io/)，通过`Ctrl+``打开命令行，并且在命令行里输入下面的内容：

```python
import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print 'Please restart Sublime Text to finish installation'
```

![](/thumbnails/go-sublime/1.png)

出现`Please restart Sublime Text to finish installation`后，重启Sublime text，然后在菜单`Preferences`下面会多出一个`Package Control`菜单项。

### Install go sublime and go build

按住`Ctrl+Shilft+p`弹出对话框中输入`install`，然后回车

![](/thumbnails/go-sublime/2.png)

在弹出的对话框里输入`go sublime`然后回车安装。

![](/thumbnails/go-sublime/3.png)

同样的方法安装`go build`

### Test

* `Ctrl +s` 保持代码的时候自动`go fmt`格式化代码。

* `Ctrl + b` 命令行，可以允许`go run go file name`运行。
