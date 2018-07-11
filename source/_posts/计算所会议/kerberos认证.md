---
layout: artical
title: kerberos认证浅析
tags: 密码学
categories: 计算所
---
# 什么是kerberos
Kerberos是个用于安全认证的第三方协议，它采用了传统的共享密钥的方式，实现了在网络环境不一定保证安全的环境下，Client和Server之间的通信。
# kerberos协议基本原理
![](/img/kerberos_0.png)
A与B之间有一个密钥key(A,B)，这个密钥只有它们两者拥有。
当A需要向B证明它是A时，发送两条信息。
一条为A的用户信息的明文，另一条为使用密钥key加密过的明文信息。
B接收后，用密钥key进行解密，然后将解密后的信息与明文信息比较。
如果他们是一样的，则表明对方就是A。从而，B确认A的身份。
# 引入KDC：key(A,B)从何而来
为了保证认证的安全性，key(A,B)应该是有时效性的，不能是固定的。
因此KDC在整个认证系统中起共同信任的第三方作用。
![](/img/kerberos_1.png)
## 大概流程
* A向KDC发出申请，申请中包含了A的身份信息
* KDC查询白名单，如果A存在则向A发送两条信息
	* 第一条为Encrypt(key(A,B), Pubkey_A)，使用A的公钥加密的key(A,B)
	* 第二条为Encrypt(A_Info+key(A,B), Pubkey_B)，使用B的公钥加密的A_Info与key(A,B)信息
* A使用私钥对Encrypt(key(A,B), Pubkey_A)信息解密，得到key(A,B)
* A开始向B进行认证申请，发送两条信息
	* 第一条为Encrypt(A_Info+Timestamp, key(A,B))，使用key(A,B)加密的A_Info和时间戳信息
	* 第二条为Encrypt(A_Info+key(A,B), Pubkey_B)
* B接收到A发送的信息后：
	* 先用私钥解密Encrypt(A_Info+key(A,B), Pubkey_B)，得到key(A,B)
	* 然后用Key(A,B)解码Encrypt(A_Info+Timestamp, key(A,B))，比较A_Info，相同则成功认证。
# 加时间戳的目的
B每次接收到A的认证信息后，如果发现时间戳与当前时间在范围外，则判定失效，保证安全
# Kerberos的优点
* 较高的性能。KDC参与次数少，相比较完全依赖可信赖的第三方方案比较，具有较大的性能提升
* 实现了双向验证
