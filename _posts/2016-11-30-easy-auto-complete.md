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

```javascript
<input type="text" name="query" placeholder="Search..." id="autocomplete">
```

```javascript
<script>
var options = {
    url: function(phrase) {
        return "http://mysite.com/auto?query=" + phrase ;
    },
};
$("#autocomplete").easyAutocomplete(options);
</script>
```
增加以上代码就可以实现所需要的效果。如果remote的json格式比较复杂，还需要增加return type的分析。可以参考http://easyautocomplete.com/guide，非常详细。为了实现下拉框还需要在header中添加必要的`css`和`js`文件。
