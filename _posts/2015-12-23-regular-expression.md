---
layout: post
title:  " 正则表达式i"
date:   2015-12-123 19:13:05
categories: JavaScript
excerpt: JavaScript
---

* content
{:toc}

概述 RegExp 构造函数可创建一个正则表达式对象，用特定的模式匹配文本。

##语法
RegExp(pattern [, flags])
/pattern/flags

##参数

pattern 正则表达式文本 flags 该参数可以是下面几个值的任意组合：
g 全局匹配
i 忽略大小写
m 让开始和结束字符（^ 和 $）工作在多行模式（也就是，^ 和 $ 可以匹配字符串中每一行的开始和结束（行是由 \n 或 \r 分割的），而不只是整个输入字符串的最开始和最末尾处。

##练习
验证姓名（英/中文）/^[a-z]{1,20}(\/[a-z]{1,20})?\/[a-z]{1,20}$|^[\u4e00-\u9fa5]{1,5}.?[\u4e00-\u9fa5]{1,5}$/ig
验证手机号 /^1\d{10}$/
验证邮箱 /^\w{3,}\@\w{2,10}\.com$/