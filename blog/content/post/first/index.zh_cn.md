---
title: "First"
description: 
date: 2023-11-10T15:34:23+08:00
image: test.jpg
math: 
license: 
hidden: false
comments: true
draft: true

---

# 对服务器网络问题的总结

最近使用实验室linux服务器跑代码，对其中频繁出现的网络问题烦不胜烦，诸如git和pip问题层出不穷，其中的问题不是简单换一个清华源可以，根源性需要让服务器走**中转代理**，实现完全性**翻墙**。

现在把其中常见的命令总结如下，方便以后快捷使用。

## git的代理设置

```bash
git config --global http.proxy socks5 127.0.0.1:7890 #端口号参考clash
git config --global https.proxy socks5 127.0.0.1:7890 #端口前面的ip地址参考开clash的主机ip


git config --global https.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890  
```

如果报错提示不让写入修改，在最后加入`--replace-all`，代码如下

```bash
git config --global https.proxy 127.0.0.1:7890 --replace-all
git config --global https.proxy 127.0.0.1:7890 --replace-all
```

*TEST*

![](test.jpg)



*备注*

如果无法正常部署博客到github.io，请使用如下命令

```bash
hugo -D
hugo --gc --minify --cleanDestinationDir
```