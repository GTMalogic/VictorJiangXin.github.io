---
layout: artical
title: python3安装pymssql
date: 2018-07-22 18:57:46
tags: 
	- python
	- pymssql
categories: 笔记
---
# 提要
由于在做项目中，需要通过python操作SQL Server数据库，在查看相关资料后，发现可以使用pymssql包，对数据库进行操作。本人使用的Python版本为3.5，查阅多种资料，都是推荐使用 `pip install pymssql` 但是，每次安装的时候，都出现错误，后来又尝试直接从pypi.org网站下载pymssql的包进行安装，进入相关包的路径后，输入`python setup.py install`但是仍旧是一堆错误。
# 解决
在查看各种博客后，最终找到了解决方法。在微软的帖子里 [微软的SQL帮助](https://docs.microsoft.com/en-us/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development?view=sql-server-2017])核心原因是，pip安装的程序需要再专门的网站里下载，即要下载对应版本的数据。[下载地址](https://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql) 我是python3.5,64位，因此下载了 `pymssql‑2.1.4.dev5‑cp35‑cp35m‑win_amd64.whl` 然后进入下载的文件夹，运行指令 `pip install pymssql‑2.1.4.dev5‑cp35‑cp35m‑win_amd64.whl`从而成功安装。问题顺利解决 
