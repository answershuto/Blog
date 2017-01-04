---
layout: post
title:  "fetch无法携带用户登陆session（cookie）的问题"
date:   2017-01-04 20:52:00 +0800
categories: jekyll update
tags: [nodeJS] 
excerpt: fetch无法携带用户登陆session（cookie）的问题
---

#  fetch无法携带用户登陆session（cookie）的问题

第一次用fetch模块来操作ajax请求，在写登陆交互的时候遇到了这个问题。

登陆成功以后服务端用setcookie的方法为客户端设置了session回话(即cookie)。但是第二个请求发过去服务端却返回了没有登陆的信息，即登陆状态异常。

排查了服务端代码以后发现没有问题。在客户端用jquery的$.ajax尝试以后发现能正常得到session并在第二个请求中携带session，服务端正常响应，确定是客户端的问题而且是fetch模块的问题。

查看文档以后解决了该问题。

详细可以查看fetch文档。

[https://github.com/github/fetch/blob/c23c3f9b3aa3e2bcc8853cc0b1b08830068e6163/README.md#sending-cookies](https://github.com/github/fetch/blob/c23c3f9b3aa3e2bcc8853cc0b1b08830068e6163/README.md#sending-cookies)

> ![img](/img/fetch/fetch.png)

必须设置credentials参数才能自动发送当前域的cookie。