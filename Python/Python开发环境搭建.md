---
title: Python使用virtualenv作为开发环境
categories: Python   
tags: Python
keywords: [Python]
description: Python使用virtualenv作为开发环境
---

[TOC]



# Python使用virtualenv作为开发环境

python开发中由于使用很多第三方库, 会不断增加引入的库, 作为程序员, 开发环境总会被搞东搞西的, 有很多库需要安装. 为了是项目环境干净整洁, 并能够精确地导出项目所依赖的库, 在开发环境中使用virtualenv就灰常方便了



## 基本步骤

* 安装virtualenv

  `pip install virtualenv`

* 设置virtualenv

  ```shell
  # 进入项目目录
  cd project
  # 设置项目需要的python版本
  virtualenv -p /usr/bin/python2.7 venv
  # 设置项目的python环境
  source venv/bin/activate
  # 此时基本完成, 若使用pycharm编辑器, 选择其运行环境
  
  # 退出环境
  deactivate
  ```
