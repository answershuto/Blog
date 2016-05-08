---
layout: post
title:  "在mac下安装MongoDB"
date:   2016-05-08 22:11:00 +0800
categories: jekyll update
tags: [搭建环境]
excerpt: MongoDB的安装搭建过程以及启动方法的记录.
---

本文主要记录我在mac上搭建MongoDB数据库环境的过程.

1.首先执行

		$ brew update

这个更新可能需要花一些时间，具体看多久没更新了。

2.接着执行

		$ brew install mongodb

安装MongoDB，显示如下打印，这个速度比较快的。

		==> Downloading https://homebrew.bintray.com/bottles/mongodb-3.2.6.el_capitan.bo
Already downloaded: /Library/Caches/Homebrew/mongodb-3.2.6.el_capitan.bottle.tar.gz
		==> Pouring mongodb-3.2.6.el_capitan.bottle.tar.gz
		==> Caveats
		To have launchd start mongodb now and restart at login:
		  brew services start mongodb
		Or, if you don't want/need a background service you can just run:
		  mongod --config /usr/local/etc/mongod.conf
		==> Summary
		🍺  /usr/local/Cellar/mongodb/3.2.6: 17 files, 263.8M

3.如果要卸载MongoDB的话执行

		$ brew uninstall mongodb

打印

		Uninstalling /usr/local/Cellar/mongodb/3.2.6... (17 files, 263.8M)

4.接着执行

		$ mongod --config /usr/local/etc/mongod.conf 

这句话是根据/usr/local/etc/mongod.conf来对MongoDB进行配置,这个我一开始执行的时候一直很慢卡在这一步了，但是把终端关闭然后重新打开以后发现一下子就执行好了。

5.接着就可以运行MongoDB了

		$ mongo
		MongoDB shell version: 3.2.6
		connecting to: test
		Server has startup warnings: 
		2016-05-08T22:04:58.899+0800 I CONTROL  [initandlisten] 
		2016-05-08T22:04:58.900+0800 I CONTROL  [initandlisten] ** WARNING: soft rlimits too low. Number of files is 256, should be at least 1000
		> 







