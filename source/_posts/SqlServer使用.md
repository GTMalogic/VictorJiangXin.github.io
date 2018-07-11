---
layout: artical
title: SqlServer使用
date: 2018-04-10 15:01:52
tags:
	- 数据库
	- SQL Server
categories: 笔记
---
# Sql Server相关  
在使用Sql Server进行存储时，碰到两个巨大的坑！  
* 一个就是关于数据库版本的问题，SQL Server在WIN7系统上无法运行2017的版本，智能运行2014的版本。  
* 一个就是SQL Server的自身版本问题，SQL Server有两种，一种是EXPRESS版，它最多只能存储10G的数据，一种是企业版，它能存储不限量的数据。

# SSMS连接SQL Server数据库  
在使用SQL Server时，我们常常使用SSMS操作数据库，对数据库进行可视化的操作。在使用SSMS操作数据库时，首先要进行数据库连接
我的数据库是默认安装的，命名为SQ2，但是，在填写数据库服务器名时，我填写SQ2，却一直无法连接到数据库，最后才发现，要使用localhost\SQ2作为名字，才可以进行连接！