---
layout: post
title:  "Mac上安装Scrapy中的问题与解决方法"
comments: true
categories: ["diary", "python", "life"]
description: "解决在安装Scrapy中遇到的小困难"
---

之前在别的电脑上配置过scrapy，后来换了mac一直没有写过爬虫，今天跟着官方指南走的时候发现安装失败很久，代码里显示`ImportError: No module named scrapy`意识到安装可能出了一些问题。

Stackoverflow上面[提到这个问题](http://stackoverflow.com/questions/10570635/scrapy-importerror-no-module-named-items)可能是由于文件命名导致的，但是我的文件命名并没有这个问题，于是就寻求别的解决办法，打算重新安装scrapy包。

按照[Scrapy安装指南](http://scrapy-chs.readthedocs.io/zh_CN/latest/intro/install.html)上的代码

    xcode-select --install
    echo "export PATH=/usr/local/bin:/usr/local/sbin:$PATH" >> ~/.zshrc
    source ~/.zshrc
    brew install python
    brew update; brew upgrade python
    pip install Scrapy

如果用的是mac自带bash，应当把上述代码中的`zshrc`替换为`bashrc`.

在重新安装的过程中，显示
    
    OSError: [Errno 1] Operation not permitted: '/var/folders/6t/h404bjcd5tb_4q86tpv_251rv_0h0j/T/pip-sYsqDS-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six-1.4.1-py2.7.egg-info'

这样的代码错误。在查询github上的[解决方法](https://github.com/pypa/pip/issues/3165 )之后，发现这似乎是El-Capitan系统的一个通病。办法就是 `pip install scrapy --ignore-installed six`。如果出现权限问题的话，就修改代码为`sudo -H pip install scrapy --ignore-installed six`。

至此，我发现scrapy包已经安装正常，但是在爬虫代码运行的时候会报错`ImportError: cannot import name xmlrpc_client`。于是在Stackoverflow上面看到[办法](http://stackoverflow.com/questions/30964836/scrapy-throws-importerror-cannot-import-name-xmlrpc-client)是需要重新安装`six`代码如下

    sudo rm -rf /Library/Python/2.7/site-packages/six*
    sudo rm -rf /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six*
    sudo pip install six

很多人评论说这样就可以解决问题了，然而我的电脑却仍然报错显示`Operation not permitted`。仍然是在Stackoverflow上的[解答](http://stackoverflow.com/questions/32659348/operation-not-permitted-when-on-root-el-capitan-rootless-disabled),让我找到了最终的解决方案。那就是在重启电脑之后，不停的按住`cmd+R`进入Recovery界面，在工具中选择终端Terminal，然后输入如下代码

    csrutil disable
    reboot

一切都解决了。