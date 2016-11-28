---
title: "Python+sae开发微信公众平台后台简明教程"
layout: post
date: 2016-07-20 20:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- develop
- diary
- python
blog: true
author: You Li
description: 微信后台配置笔记：Webpy＋SAE
---

## 前言

笔者本来并没有写攻略的习惯，因为在Google上找到的教程大多数都比较靠谱，没有必要自己仿照他们的重新写一遍。但是这几天为了实验室一个要上线的项目折腾了几天的微信后台开发，意识到了大多数网络上的教程都是抄来抄去，甚至连代码中的错误都一模一样，作者们似乎都没有自己测试过。因此决定记录一下最简单的微信后台开发方法。本文采用Python语言，笔者也不是非常熟悉，正在学习的过程中，用php或者java做后台都可以，原理应该都差不多，也许读了本文就不需要再去读微信公众平台开发者文档了（还是建议读一下）。

闲话少说，我们开始介绍整个流程。申请公众平台并且开启开发者模式应该是比较容易的，所以在这里不再赘述，让我们直接进入配置服务器的阶段。

## 后台服务器的配置

点击修改配置，我们看到微信平台要求我们提供服务器地址URL，令牌Token和EncodingAESKey才可以开启服务器。对于还没有服务器的同学，可以申请一个新浪云sae调用他们的服务器地址（免费的，功能也还不错）。

简单注册之后，可以创建一个新应用，语言选择Python2.7。托管代码的方式有git和SVN供选择，笔者选择的是后者（因为从来没有用过觉得好新奇），使用git比较熟练的同学也可以使用前者，管理起来应该比较方便。当然了，选择SVN可以同时管理多个版本，并且新浪云还内嵌了在线编辑器，方便了很多。

创建默认版本1，就可以得到一个默认版本以及你的url`http://1.xxxx.applinzi.com`（但是这个url可能和你要提供给微信的url不一样），其中xxxx代表你的web应用名称。点击“编辑代码”,可以看到默认的代码文件是`index.wsgi`和`config.yaml`。下面我们为了能够让微信后台验证我们的服务器地址，我们需要添加一些代码。

在`config.yaml`文件中，在name和version之下添加代码

    libraries:
    - name: webpy
      version: "0.36"
    - name: lxml
      version: "2.3.4"

可以看出我们将要调用webpy和lxml包。接下来我们更改`index.wsgi`中所有的代码如下

    #coding: UTF-8
    import os

    import sae
    import web

    from weixinInterface import WeixinInterface

    urls = (
    '/weixin','WeixinInterface'
    )

    app_root = os.path.dirname(__file__)
    templates_root = os.path.join(app_root, 'templates')
    render = web.template.render(templates_root)

    app = web.application(urls, globals()).wsgifunc()
    application = sae.create_wsgi_app(app)

其中我们相当于把我们的服务器地址设在了`http://1.xxxx.applinzi.com/weixin`（这也就是微信后台所需要提供的服务器地址）。注意到，我们这里提到了一些其它的文件，比如`'templates'`和`'WeixinInterface'`。接下来就在代码编写页面创建空文件夹`templates`虽然我们暂时用不到，但是之后文件夹中将会放上`.xml`文件方便我们在调用不同的后台回复方式。

同时创建`weixinInterface.py`文件，注意，不是上面所提到的`WeixinInterface`，有大小写区别（当然你可以随便起一个喜欢的名字），这个文件中将调用名为后者的类，所以请不要弄混。
创建该文件之后我们需要在其中添加GET方法从而可以验证Token验证服务器。

    # -*- coding: utf-8 -*-
    import hashlib
    import web
    import lxml
    import time
    import os
    import urllib2,json
    from lxml import etree

    class WeixinInterface:

        def __init__(self):
            self.app_root = os.path.dirname(__file__)
            self.templates_root = os.path.join(self.app_root, 'templates')
            self.render = web.template.render(self.templates_root)

        def GET(self):
            #获取输入参数
            data = web.input()
            signature=data.signature
            timestamp=data.timestamp
            nonce=data.nonce
            echostr=data.echostr
            #自己的token
            token="YourToken" #这里改写你在微信公众平台里输入的token
            #字典序排序
            list=[token,timestamp,nonce]
            list.sort()
            sha1=hashlib.sha1()
            map(sha1.update,list)
            hashcode=sha1.hexdigest()
            #sha1加密算法

            #如果是来自微信的请求，则回复echostr
            if hashcode == signature:
                return echostr

这里的`YourToken`可以随便起一个喜欢的名字，只要和微信公众平台后台服务器配置那里输入的Token一样就可以了。现在基本就已经配置完毕了，可以保存了之后回到微信后台界面，输入URL和Token，并且随机生成一个明文Key，服务器配置应该就可以通过了。

如果显示Token验证失败，可能是代码问题，请到你的应用网址`http://1.xxxx.applinzi.com`查看log判断代码错误位置。一般可能是由于缩进导致的错误。

## 实现最简单的后台回复功能

在微信后台开发者文档中我们可以看到，回复消息的时候我们有多种可选方式，比如文本消息，图片消息，图文消息，语音消息和视频消息。笔者还没有测试过所有的，现在只测试了文本消息的回复，其它的都大同小异。在文档中给出了相应的`.xml`文件代码，稍加修改就可以适用。

我们先来实现一个最简单的功能，就是重复发送者的消息。我们先在刚刚创建的空文件夹`templates`中创建文件`reply_text.xml`并且在里面增加代码

    $def with (toUser,fromUser,createTime,content)
    <xml>
    <ToUserName><![CDATA[$toUser]]></ToUserName>
    <FromUserName><![CDATA[$fromUser]]></FromUserName>
    <CreateTime>$createTime</CreateTime>
    <MsgType><![CDATA[text]]></MsgType>
    <Content><![CDATA[$content]]></Content>
    </xml>

这种封装方式是webpy中的，可以方便我们在主程序中调用。从第二行开始的代码则是在开发者文档中给出的。

我们回到主程序`weixinInterface.py`，在class中再添加一个POST方法（和GET方法同缩进），代码如下

    def POST(self):
            str_xml = web.data() #获得post来的数据
            xml = etree.fromstring(str_xml)#进行XML解析
            content=xml.find("Content").text#获得用户所输入的内容
            msgType=xml.find("MsgType").text
            fromUser=xml.find("FromUserName").text
            toUser=xml.find("ToUserName").text
            return self.render.reply_text(fromUser,toUser,int(time.time()), u"我现在的功能可以重复你的消息 ："+content)

这段代码中显然我们没有处理我们得到的消息，而是直接获得之后就返回给对方。如果我们想设置口令，返回给用户特定消息怎么办？可以将代码修改如下

    def POST(self):
            str_xml = web.data() #获得post来的数据
            xml = etree.fromstring(str_xml)#进行XML解析
            msgType=xml.find("MsgType").text
            fromUser=xml.find("FromUserName").text
            toUser=xml.find("ToUserName").text
            if msgType == 'text':
                content = xml.find("Content").text
                if content == 'help':
                    #replayText = "hello hello hello"
                    return self.render.reply_text(fromUser, toUser, int(time.time()), "随便看看？（对不起我功能有限QAQ）")
                else:
                    return self.render.reply_text(fromUser, toUser, int(time.time()), "哎呀出错了 输入个help看看如何正确的调戏我？")

这里的处理相当于判断了发送者的消息类型，如果是`'text'`，又是`help`那么就输出 `随便看看？（对不起我功能有限QAQ）`，其它情况输出 `哎呀出错了 输入个help看看如何正确的调戏我？`。这里建立口令的方法非常简单，大家可以随心所欲不逾矩。

仿照上面的方法，也就不难设置图文消息的回复了。回复图片、语音、视频需要MediaID，可能需要调用access_token，笔者没有尝试，这里也就先不表。

## 设置欢迎用户消息

当有用户订阅了公众号之后，我们需要设置回复模版。和上面相似的是，我们回复的仍然是文本消息，但是需要我们判断接受的消息不再是用户输入的文本Text而是一个Event，因此我们需要添加对于Event事件的代码。

和`if msgType == 'text':`并列，可以继续添加下列代码

    if msgType == 'event':
            if xml.find("Event").text == 'subscribe':#关注的时候的欢迎语
                return self.render.reply_text(fromUser, toUser, int(time.time()), u"谢谢你的关注，输入help看看如何正确的调戏我")

这样就可以实现发送欢迎消息的功能了。

## 注

在线编辑器下的代码缩进很容易错误，所以建议大家可以用PyCharm先把代码同步到本地再处理代码（直接从命令行调SVN版本当然也是可以的）。如果出现了错误，请利用应用网页的日志来进行调试。

还有别的问题欢迎留言给我一起讨论。
