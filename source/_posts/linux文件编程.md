---
layout: artical
title: linux文件编程
date: 2018-04-26
tags: 
	- linux 
	- 文件操作
categories: linux学习
---
# 概述
Linux系统讲所有设备都当作文件来处理，因此Linux文件编程极其重要！
linux文件编程设计到文件的读、写定位等各种操作。其中对文件的操作都只能由内核态完成，用户态想要访问文件只能通过系统调用。
Linux文件编程中，最重要的一个概念便是文件描述符fd，所有文件的操作都通过文件描述符进行，每个文件对应一个文件描述符。
打开现存文件或新建文件时，内核会返回一个文件描述符，读写文件也需要使用文件描述符。  
# 需要添加的函数库
```
#include <fcntl.h>	//文件控制选项头文件
#include <unistd.h> //针对系统调用封装
#include <sys/type.h>
```
# 文件操作
## open 打开文件
函数原型： `int open(char *name, int how)`
功能： 打开一个文件
* name: 文件名
* how: 文件操作限制
	* O_RDONLY 只读
	* O_WRONLY 只写
	* O_RDWR 读写  
* return 
	* -1: 打开失败
	* 非零: 对应的文件描述符  

## create 创建文件
函数原型： `int fd = create(char *filename, mode_t mode)`
功能： 创建一个文件，如果源文件存在，则讲该文件清空
* filename： 文件名
* mode: 访问模式
	* COPYMODE
* return
	* -1: 打开失败
	* 非零: 成功创建

## read 读数据
函数原型: `ssize_t read(int fd, void *buf, size_t qty)`
功能： 读取qty长度的数据到缓存buf中
* fd: 所读文件的文件描述符
* buf: 缓存
* qty: 读取文件的长度
* return:
	* -1: 读取错误
	* 非零: 读取的长度

## write 写数据
函数原型： `ssize_t write(int fd, void *buf, size_t amt)`
功能： 将buf中数据写入文件中
* fd: 所写文件的文件描述符
* buf: 缓存
* amt: 写进文件的长度
* return:
	* -1: 读取错误
	* 非零: 写入的长度

## lseek 定位文件指针
函数原型： `off_t lseek(int fd, off_t dist, int base)`
功能： 将文件指针定位到制定位置
* fd: 操作的文件描述符
* dist: 相对基位置的偏移量
* base: 基位置
	* SEEK_SET 文件的开始
	* SEEK_CUR 当前位置
	* SEEK_END 文件结尾
* return
	* -1: 错误
	* 其他： 指针变化前的位置

## close 关闭文件
函数原型： `int close(int fd)`
功能： 关闭文件
* fd: 需要关闭的文件
* return
	* -1: 错误
	* 0: 关闭正确

# 处理系统调用中的错误
## errno
内核通过全局变量来指定系统调用的类型，每个程序可以访问到该变量。一般不用它判断系统调用错误。
## perror
使用 `perror(string)`
系统会自己查找调用错误的类型，并且将错误信息打印。


