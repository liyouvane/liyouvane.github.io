---
title: "Ajax远程调用Json实现自动补全"
layout: post
date: 2016-11-30 16:46
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Java
- develop
- diary
- Ajax
blog: true
author: You Li
description: 自动补全下拉框的实现
---

最近一个项目做的是搜索引擎的前端，要实现的是搜索框实时匹配用户输入并且提示相关内容。我们的框架是要在得到query的term之后访问`/auto?query=<term>`并且获取网页中的json，分析数据以下拉框的方式返回，并且对匹配字符高亮。

```
response.append("<input type=\"text\" name=\"query\" placeholder=\"Search...\" id=\"autocomplete\">" + "\n");
```

```
response.append("<script>");
response.append("var options = {\n" +
    "\turl: function(phrase) {\n" +
    "\t\treturn \"http://"+host+":25804/auto?phrase=\" + phrase ;\n" +
    "\t},\n" +
    "};\n" +
    "$(\"#autocomplete\").easyAutocomplete(options);");
response.append("</script>");
```
