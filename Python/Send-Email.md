---
title: Python-Send-Email
categories: Python   
tags: Python
keywords: [Python]
description: Python Send Email

---

[TOC]

# Send-Email
## 相关Python模块
> * `smtplib` 关于SMTP(简单邮件传输协议)的操作模块, 在发送邮件的过程中起到服务器之间相互通信的作用
> * `email` 服务器之间的通信, 包括信息头, 信息主体等
*简单讲: 登录邮箱, 发送邮件是由SMTP接管; 写邮件,添加附件是由email模块控制 *
 
## 基本思路
> * 设置好服务器端信息
> * 邮件主体信息
> * 登录发送 (处理文本时需要使用MIMEText类)
 
## 简单代码

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Time    : 16/12/6 08:36
# @Author  : Snaker95
# @Desc    : 
# @File    : emai.py
# @Software: PyCharm

import smtplib
from email.mime.text import MIMEText

'''
 # 设置服务器所需信息
'''
mail_host = 'smtp.163.com'
mail_user = 'lfsfxyzjg@163.com'
mail_pwd  = '**passwd**' # 163邮箱时, 这里为授权码

sender    = 'lfsfxyzjg@163.com'
receivers = ['1083601773@qq.com'] # 列表可以包含多个

message   = MIMEText('content', 'plain', 'UTF-8')
message['Subject'] = 'title'
message['From'] = sender
message['To']   = receivers[0]


try:
    smtpObj = smtplib.SMTP()
    smtpObj.connect(mail_host, 25)
    # 如果需要 SSL认证 则使用 SMTP_SSL
    # smtpObj = smtplib.SMTP_SSL(mail_host, 994)
    smtpObj.login(mail_user, mail_pwd)
    smtpObj.sendmail(
        sender, receivers, message.as_string()
    )

    smtpObj.quit()
    print 'SUCCESSS'

except smtplib.SMTPException as e:
    print 'error: %s'%e
```

## 带有附件的email
　　上面我们介绍了发送字符串的邮件，但这明显不满足现代邮件的需求，下面我们介绍如何发送内容以 HTML 格式，并且带有图片和文本文件的邮件。
　　处理多种形态的邮件主体我们需要 MIMEMultipart 类，而处理图片需要 MIMEImage 类。

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Time    : 16/12/7 09:12
# @Author  : Snaker95
# @Desc    : 
# @File    : email-file.py
# @Software: PyCharm


import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.image import MIMEImage

# 设置登录信息
mail_host = 'smtp.163.com'
mail_user = 'sendmail@163.com'
mail_pwd  = '**passwd**'
sender = 'lfsfxyzjg@163.com'
receviers = ['receviermail@qq.com']

# 设置email信息

# 1. 添加一个MIMEmultipart类, 处理正文和附件
message = MIMEMultipart()
message['From'] = sender
message['To']   = receviers[0]
message['Subject'] = '详见附件'
# 2.(1) html为正文内容
with open('abc.html','r') as f:
    content_h = f.read()
    # 设置参数
part1 = MIMEText(content_h, 'html', 'UTF-8')
print part1

# 2.(2) txt文本为附件
with open('abc.txt','r') as f:
    content_t = f.read()
    # 设置参数
part2 = MIMEText(content_t, 'plain', 'UTF-8')
# 附件设置内容类型, 一般为二进制流
part2['Content-Type'] = 'application/octet-stream'
# 设置附件头, 添加文件名
part2['Content-Disposition'] = 'attachment;filename="abc.txt"'

# 2.(3) 添加照片为附件
with open('1.png','rb') as f:
    pic = MIMEImage(f.read())

pic['Content-Type'] = 'application/octet-stream'
pic['Content-Disposition'] = 'attacment;filename="1.png"'

# 3. 将内容附加到邮件主体
message.attach(part1)
message.attach(part2)
message.attach(pic)


# 4. 登录并发送
try:
    smtpObj = smtplib.SMTP()
    smtpObj.connect(mail_host,25)
    smtpObj.login(mail_user, mail_pwd)
    smtpObj.sendmail(
        sender, receviers, message.as_string()
    )
    print 'SUCCESS'
    smtpObj.quit()

except smtplib.SMTPException as e:
    print 'ERROR : %s' %e
```

## 注意事项
> * 使用脚本发送邮件, 需开通电子邮箱的SMTP功能

Author [@Snaker95][1]

[1]: http://www.sharedsea.com