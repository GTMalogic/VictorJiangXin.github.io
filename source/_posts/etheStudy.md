---
layout: artical
title: 通过geth Json-Rpc接口遇到的坑
date: 2018-07-19 16:18:48
tags: 
	- 以太坊
	- Geth
	- Json-RPC
categories: 区块链技术
---

# 使用Python访问Geth遇到的问题
在使用python对Geth提供的Json-RPC服务器进行访问时，一直返回由于目标计算机积极拒接、无法连接的错误，因此作出以下尝试：
1. 使用python访问百度等官方网站，经测试，可以访问，说明当前配置无问题，问题出现在服务器端。
2. 进入服务器，用netstat | grep 8545查看相关端口状态，发现端口一直处于Stopped状态。geth无法正常运行。经检测是磁盘空间不足，Geth数据无法同步，导致Geth运行失败，停止尝试。将.ethereum文件夹全部删除，重新运行geth --rpc，发现8545端口正常运行。
3. geth虽然端口正常运行，但是python仍旧无法去访问JsonRPC服务器。于是运行另一种geth命令。 geth --rpc --rpcaddr 10.21.41.1 --rpcport 8545。运行后，python能够从geth客户端获取信息问题解决。
4. 问题原因，一开始访问 (ethereum JSONRPC的WIKI)[https://github.com/ethereum/wiki/wiki/JSON-RPC]里面提供了两种运行命令：
`geth --rpc`
该种默认rpcaddr是localhost.rpcport是8545。
`geth --rpc --rpcaddr <xx> --rpcport <port>`
该种情况是定义了IP和端口号，我以为localhost应该就是服务器的内网IP即 10.21.41.1但是，实际python访问是不行的，必须定义好rpcaddr，定义它为geth所在服务器的IP地址，从而才可以访问。

