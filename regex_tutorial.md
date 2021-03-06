---
title: "正则表达式基础总结"
author : "Jun"
date: 2021-03-05T20:32:06+08:00
categories : ["编程"]
tags : ["正则表达式,编程"]
---
## 什么是正则表达式
正则表达式简单来理解就是一个字符串，它可以看作一个规则．通过它我们可以提取一个文本中一些具有相同模式的字符串，或者验证所给字符串是否符合要求等．
比如我们想提取一个文件中所有的电话号码，或者前端判断所给数据，如身份证号码是否合法，有无少输等，这时候正则表达式便起到了很大作用．

## 元字符

| 元字符  | 含义                                  |
| ------- | ------------------------------------- |
| `.`     | 匹配任意单个字符除了换行符            |
| `*`     | 匹配>=0个重复的字符                   |
| `+`     | 匹配>=1个重复的字符                   |
| `$`     | 表示末尾                              |
| `^`     | 表示开头                              |
| `|`     | 或运算符，匹配符号前或后的字符        |
| `?`     | 非贪婪匹配                            |
| `\`     | 转义字符,用于匹配一些保留的字符       |
| `(xyz)` | 字符集，匹配与 `xyz `完全相等的字符串 |
|`{m,n}`|匹配m到n个大括号之前的字符|



## 字符集

我们可以用`[]`来指定一个字符集，用连字符来指定字符集的范围

- `[a-z]`表示匹配所有小写字母
- `[0-9]`表示匹配所有数字

### 否定字符集

`^`用在字符集中的时候，表示否定的意思

[^0-9]表示匹配所有非数字

### 简写字符集

正则表达式内置了一些常用的字符集

| 名称 | 含义               |
| ---- | ------------------ |
| `\w` | 匹配所有字母数字   |
| `\W` | 匹配所有非字母数字 |
| `\d` | 匹配数字           |
| `\D` | 匹配非数字         |
| `\n` | 匹配一个换行符     |
| `\r` | 匹配一个回车符     |
| `\t` | 匹配一个制表符     |
| `\s` | 匹配所有空格字符   |
| `\S` | 匹配所有非空格字符 |





## 重复

- `*`表示重复次数大于等于0
- `+`表示重复次数大于等于1
- `?`表示重复次数为0或1
- `{n}`表示重复n次，`{m,n}`表示重复m到n次



## 特征标群

`(...)`表示括号中的所有字符不可分割，而是作为一个整体参加匹配



## 常用正则表达式

### 邮箱

```
^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$
```

### 手机号码

```
^1(3|4|5|6|7|8|9)\d{9}$
```

### 域名

```
^((http:\/\/)|(https:\/\/))?([a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?\.)+[a-zA-Z]{2,6}
```

### IP

```
((?:(?:25[0-5]|2[0-4]\d|[01]?\d?\d)\.){3}(?:25[0-5]|2[0-4]\d|[01]?\d?\d))
```



## 引用文献

- [common-regex](https://github.com/cdoco/common-regex)
- [正则表达式30分钟入门教程](https://deerchao.cn/tutorials/regex/regex.htm)
- [learn-regex](https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md#21-%E7%82%B9%E8%BF%90%E7%AE%97%E7%AC%A6-)

