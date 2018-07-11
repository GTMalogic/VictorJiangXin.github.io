---
title: linux获取时间及Socket编程
layout: artical
tags: linux
categories: 笔记
---
# 如何获取时间
UNIX时间戳：从格林威治时间1970年01月01日00时00分00秒起至现在的总秒数。  
UTC时间：世界协调时。以地球自转为基础的时间标准。 UTC表示方式为 年月日时分秒。

## 获取UNIX时间戳
C运行库：`#include <sys/time.h>`
运行环境： C语言运行环境  
函数： `gettimeofday(struct timeval* tv, struct timezone* tz)`
功能： 获取当前UNIX时间戳  
相关结构体：  
```
	struct timeval{
		long tv_sec;	//seconds
		long tv_usec;	//microSeconds
	}

	struct timezone{
		int tz_minuteswest;	//diff of greenwich
		int tz_dsttime;	//type of DST correction
	}
```
## 获取UTC时间
C运行库：`#include <time.h>`   
运行环境： C语言运行环境  
函数1： `char *ctime(const time_t *timer)`   
功能1： 将UNIX时间戳转化成   Www Mmm dd hh:mm:ss yyyy (Mon Aug 13 08:23:14 2012)形式  
函数2： `struct tm *gmtime(time_t *timer)`  
功能2： 将UNIX时间转化成struct tm结构。
相关结构体：  
```
	struct tm{
		int tm_sec;
		int tm_min;
		int tm_hour;
		int tm_mday;
		int tm_mon;
		int tm_year;
		int tm_wday;
		int tm_yday;
		int tm_isdst;
	}
```
注意： UTC时间年是以1900为参考点，记时间时需要year+1900， 时区是0时区，如果按照北京时间（东8区）需要tm_hour+8  

测试代码：  
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <sys/time.h>
#include <time.h>

struct tm *GetBeijingTime();
int main(){
	struct tm *beijingTime;
	beijingTime = GetBeijingTime();
	printf("%d/%d/%d %2.2d:%2.2d:%2.2d\n", beijingTime->tm_year,
		beijingTime->tm_mon, beijingTime->tm_mday, beijingTime->tm_hour,
		beijingTime->tm_min, beijingTime->tm_sec);
}

struct tm *GetBeijingTime(){
	struct timeval tv;
	struct tm *BeijingTime;
	gettimeofday(&tv, NULL);
	BeijingTime = gmtime((time_t*)&(tv.tv_sec));
	BeijingTime->tm_year += 1900;
	BeijingTime->tm_hour += 8;
	BeijingTime->tm_mon += 1;
	return BeijingTime;
}
```
# Socket编程
包含的库：  
```
#include <sys/socket.h>
#include <netinet/in.h>
```
## 服务器端
* 创建socket `int socket(int domain, int type, int protocol)`
	* domain: 协议域，决定了socket的地址类型。
		* AF_INET: 用于IPV4网络通信
		* AF_UNIX: 单一Unix系统中进程间通信
		* AF_INET6: 用于IPV6网络通信
	* type: 制定socket类型。
		* SOCK_STREAM: 流式，一般用于TCP通信
		* SOCK_DGRAM: 数据包式，一般用于UDP通信
	* protocaol: 制定协议。 IPPROTO_TCP表示TCP协议，IPPROTO_UDP表示UDP协议，为0时自动选择type默认协议
	* return: -1 错误； 非负值 成功
* 绑定socket和端口号 `int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen)`
	* sockfd: socket返回的套间字描述符，类似于文件描述符。
	* addr: 有个sockaddr类型数据的指针，指向的时被绑定结构变量
	```
	//IPV4的sockaddr地址结构
	struct sockaddr_in{
		sa_family sin_family;	//协议类型，AF_INET
		in_port_t sin_port;		//端口号
		struct in_addr sin_addr;	//ip地址
	};
	struct in_addr{
		uint32_t s_addr;
	}
	
	//有关于sockaddr的相关函数
	htons() 将整形类型转换成网络字节序 比如 htons(INADDR_ANY),让系统自动获取本地IP地址
	htonl() 将长整形转换为网络字节序
	inet_pton(int af, char *str, pvoid addrbuf) 将点分十进制IP地址转换成网络字节
		af: 协议域，与socket的domain一致
		str: 要转换的地址 127.0.0.1
		addrbuf: sinaddr的指针
	inet_ntop(int af, pvoid addrbuf, char *str, size_t len) 将网络字节序转换成分十进制IO地址
		af: 协议域，与socket的domain一致
		addrbuf: sinaddr的指针
		str: 转换成的地址
		len: 字节单位长度
	
	```
	* addrlen: 地址长度
* 监听端口号 `int listen(int sockfd, int backlog)`
	* sockfd: 要监听的sock描述字
	* backlog: socket可以排队的最大连接数
* 接收用户请求 `int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen)`
	* sockfd: 服务器socket描述字
	* addr: 地址指针，保存用户对应的地址
	* addrlen： 地址长度。保存用户地址的长度
	* return: 一旦accept某个客户机请求成功，返回一个新的描述符，用于标识具体客户的TCP连接。
* 从socket中读取字符 `ssize_t read(int fd, void *buf, size_t count)`
	* fd: 读取的用户对应的套间字描述字
	* buf: 保存接收信息的缓冲区
	* count: 读取的长度
	* return: 如果大于零表示读取的长度，返回0表示文件读取结束，小于0表示发生错误
* 向socket发送信息 `ssize_t write(int fd, const void *buf, size_t count)`
	* fd: 发送的用户对应的套间字
	* buf: 缓冲区
	* count: 发送的信息长度
	* return: 如果大于零表示发送的长度，小于0表示发生错误
* 关闭socket `int close(int fd)`
	* fd: accept返回的连接描述字，每个连接有一个，生命周期为连接周期。
	* ps: sockfd是监听描述字，一个服务器只有一个，用于监听是否有连接；fd是连接描述字，用于每个连接的操作。
## 客户端
* 创建socket `int socket(int domain, int type, int protocol)`
	* domain: 协议域，决定了socket的地址类型。
		* AF_INET: 用于IPV4网络通信
		* AF_UNIX: 单一Unix系统中进程间通信
		* AF_INET6: 用于IPV6网络通信
	* type: 制定socket类型。
		* SOCK_STREAM: 流式，一般用于TCP通信
		* SOCK_DGRAM: 数据包式，一般用于UDP通信
	* protocaol: 制定协议。 IPPROTO_TCP表示TCP协议，IPPROTO_UDP表示UDP协议，为0时自动选择type默认协议
	* return: -1 错误； 非负值 成功
* 连接指定服务器 `int connect(int sockfd, struct sockaddr *addr, socklen_t addrlen)`
	* sockfd：客户端的sock描述字
	* addr: 服务器的地址
	* addrlen: socket地址长度
* 向socket发送信息 `ssize_t write(int fd, const void *buf, size_t count)`
	* fd: 发送的用户对应的套间字
	* buf: 缓冲区
	* count: 发送的信息长度
	* return: 如果大于零表示发送的长度，小于0表示发生错误
* 关闭socket `int close(int fd)`
	* fd: accept返回的连接描述字，每个连接有一个，生命周期为连接周期。

## 实例
客户端连接服务器，服务器向客户端发送当地时间，客户端接收并显示。  
服务器端：
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <sys/time.h>
#include <time.h>
#include <errno.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define DEFAULT_PORT 6666
#define BUF_LEN 128

struct tm *GetBeijingTime();
int main(){
	int SocketServer;
	struct sockaddr_in ServerAddr;
	char Buf[BUF_LEN];
	
	//create socket
	if((SocketServer = socket(AF_INET, SOCK_STREAM, 0)) == -1){
		printf("create socket error!\n");
		exit(0);
	}
	
	//create addr
	memset((char*)&ServerAddr, 0, sizeof(ServerAddr));
	ServerAddr.sin_family = AF_INET;
	ServerAddr.sin_addr.s_addr = htonl(INADDR_ANY);
	ServerAddr.sin_port = htons(DEFAULT_PORT);
	
	//bind the socket with address
	if(bind(SocketServer, (struct sockaddr*)&ServerAddr, sizeof(ServerAddr)) == -1){
		printf("bind socket with addr fail!\n");
		exit(0);
	}
	
	//listen
	if(listen(SocketServer, 20) == -1){
		printf("listen error!\n");
		exit(0);
	}

	printf("server: waitting for client connect.........\n");
	
	//loop for accept
	while(1){
		int SocketClient;
		struct sockaddr_in ClientAddr;
		char ClientAddrString[30];
		socklen_t ClientAddrLen;

		if((SocketClient = accept(SocketServer, (struct sockaddr*)&ClientAddr, &ClientAddrLen)) == -1){
			printf("accept socket error!\n");
			exit(0);
		}
		inet_ntop(AF_INET, &ClientAddr.sin_addr, ClientAddrString, sizeof(ClientAddrString));
		printf("server: %s connecting .....\n", ClientAddrString);

		//get the time
		struct tm *beijingTime;
		beijingTime = GetBeijingTime();
		sprintf(Buf, "TIME: %d/%d/%d %2.2d:%2.2d:%2.2d\n", beijingTime->tm_year,
		beijingTime->tm_mon, beijingTime->tm_mday, beijingTime->tm_hour,
		beijingTime->tm_min, beijingTime->tm_sec);
		
		//send the time 
		if(write(SocketClient, Buf, strlen(Buf)) == 0)
			printf("server: send message error!\n");
		
		//close the socket connect
		close(SocketClient);
		printf("server: close the client\n");
	}
	close(SocketServer);
	
	return 1;
}

struct tm *GetBeijingTime(){
	struct timeval tv;
	struct tm *BeijingTime;
	gettimeofday(&tv, NULL);
	BeijingTime = gmtime((time_t*)&(tv.tv_sec));
	BeijingTime->tm_year += 1900;
	BeijingTime->tm_hour += 8;
	BeijingTime->tm_mon += 1;
	return BeijingTime;
}
	
```
客户端：
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <sys/time.h>
#include <time.h>
#include <errno.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define BUF_LEN 128

int main(int argc, char** argv){
	int SocketClient;
	char Buf[BUF_LEN];
	struct sockaddr_in ServerAddr;
	
	//create socket
	if((SocketClient = socket(AF_INET, SOCK_STREAM, 0)) < 0){
		printf("create socket error\n");
		exit(0);
	}

	//set the server's addr
	memset((char*)&ServerAddr, 0, sizeof(ServerAddr));
	ServerAddr.sin_family = AF_INET;
	int port = atoi(argv[2]);
	ServerAddr.sin_port = htons(port);
	if(inet_pton(AF_INET, argv[1], &ServerAddr.sin_addr) <= 0){
		printf("inet_pton error!\n");
		exit(0);
	}
	
	//connect
	if(connect(SocketClient, (struct sockaddr*)&ServerAddr, sizeof(struct sockaddr)) < 0){
		printf("connect error!\n");
		exit(0);
	}
	
	memset(Buf, 0, BUF_LEN);
	//read the message from server
	if(recv(SocketClient, Buf, BUF_LEN, 0) < 0){
		printf("recv error!!\n");
		exit(0);
	}
	printf("Client: recv from server-> %s\n", Buf);
	
	//close socket
	close(SocketClient);

	return 1;
}
```


















