---
title: "编译安装GCC12"
date: 2022-01-01T12:32:17+08:00
author: "Jun"
categories: ["编程"]
tags: ["gcc", "compiler"]
---

因为想用C++20标准的原因，所以要求使用GCC12。可是Ubuntu20默认的编译器是GCC9，于是只好自己编译安装一个
GCC trunk。

没想到听起来很简单的事情，真正做起来竟然有那么多的坑，所以还是稍微写点文字记录一下。

## 下载源码
```bash
git clone https://github.com/gcc-mirror/gcc.git
```

## 配置依赖
```bash
sudo apt install flex
```

```bash
cd gcc
./contrib/download_prerequisites
```

## 编译
```bash
mkdir build && cd build

../configure -v --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --enable-checking=release --enable-languages=c,c++ --disable-multilib

make -j8
```

耐心等待编译完成，具体时间要看机器的性能。

## 安装
```bash
sudo make install
```

## 踩的坑
安装好gcc后不知道为什么在编译LLVM的时候会报错：
```bash
/lib/x86_64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.30' not found
```

使用`strings /lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCXX`可以发现确实没有`GLIBCXX_3.4.30`。
使用`ls -al /lib/x86_64-linux-gnu/libstdc++.so.6`可以发现这是一个软链接：
```
lrwxrwxrwx 1 root root 36 1月   1 13:44 libstdc++.so.6 -> libstdc++.so.6.29
```
很显然这还是旧版本的`libstdc++`，为啥会这样我也不清楚-_-

解决办法也很直接，把`libstdc++.so.6`重新链接到我们新编译的就好了：
```bash
unlink
ln -s /usr/local/lib64/libstdc++.so.6.0.30 /lib/x86_64-linux-gnu/libstdc++.so.6
```
