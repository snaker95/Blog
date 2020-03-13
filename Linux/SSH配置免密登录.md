---
title: Linux - ssh配置免密登录
categories: Linux 
tags: Linux
keywords: [Linux, ssh]
description: 总结一点, 成长一点

---

[TOC]
# Linux - ssh配置免密登录

由于开发环境大多时候使用Linux系统, 猿类都要时不时的远程连接到开发环境进行研发和调试, 但是, 要求安全性也是必不可少的,  现在大多数的登录有之前的账号密码改为公钥+密码登录; 为了方便连接, 而不每次都要输入密码, 所以需要配置ssh来简化登录流程

1. 配置ssh配置文件 ~/.ssh/config

   ```shell
   Host ssh_name
   	HostName hostname(IP)
   	User user
   	Port 22
   	IdentityFile pem_file_name
   UseKeychain yes
   ...
   # 注: pem_file_name 需要 700/600 权限
   ```

2. 倘若需要密码, 则需

   ```shell
   # ssh-add -K pem_file_name<enter>
   # Enter passphrase for pem_file_name: password
   ```

3. 若出现`could not open a connection to your authentication agent` 需要执行"eval \`ssh-agent -s\`"

4. 这样就ok了, 只需要输入`ssh ssh_name`就可以登录到所需要的服务器了
