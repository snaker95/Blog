---
title: Python中requirements.txt生成
categories: Python   
tags: Python
keywords: [Python]
description: Python中requirements.txt生成
---

[TOC]



# Python中requirements.txt生成

Python项目中依赖第三方库是不可避免, 当项目开发完成交付的时候, 需要描述清楚项目中所依赖的库, 但是由于开发周期比较长, 开发人员的流动性, 可能造成库的遗漏, 所以自动生成requirements.txt是必要的

## 方案一:

```shell
# 进入项目目录
cd project
# 生成requirements.txt
pip freeze > requirements.txt
```

## 方案二:

```shell
# 通过pipreqs, 安装并重启客户端
pip install pipreqs

# 生成requirements.txt
pipreqs ./ # 目录, 不用手动添加requirements.txt
```



## 比较 `pip freeze` / `pipreqs`

* `pip freeze` 打包整个环境中的库; 建议配合`virtualenv`使用
* `pipreqs`通过对项目目录扫描, 发现使用的类库, 存在偏差, 需要手动检查和调整

## 使用requirements.txt解决依赖

```shell
pip install -r requirements.txt
```

