---
title: Linux - ssh相关知识
categories: Linux 
tags: Linux
keywords: [Linux, ssh]
description: 总结一点, 成长一点

---

[TOC]
# Linux - ssh知识总结

## SSH 是什么

SSH 为 Secure Shell的缩写, 为建立在应用层基础上的安全协议, 相对来说是可靠的, 专为远程登录和其他网络服务提供安全性的协议.

### SSH安全验证

	* 基于口令的安全验证
* 基于秘钥的安全验证

## ssh-agent 

`ssh-agent` 是用于管SSH private keys的, 长时间持续运行的守护进程（daemon）. 唯一目的就是对解密的私钥进行高速缓存;

`ssh-add` 提示并将用户的使用的私钥添加到由`ssh-agent` 维护的列表中. 此后, 当使用公钥连接到远程 SSH 或 SCP 主机时,不再提示相关信息.

## ssh-keygen

用于为SSH生成、管理和转换认证密钥，它支持RSA和DSA两种认证密钥.

* 公钥放在服务端
* 私钥放在客户端

所以配置不同的密钥对, 来实现用户不同的需求