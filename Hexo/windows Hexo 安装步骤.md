---
title: windows Hexo 安装步骤
categories: 程序   
tags: Hexo
keywords: Hexo
description: Windows下安装Hexo的必要步骤以及简单排错

---

<!--more-->

[TOC]

# windows Hexo 安装步骤
Windows下安装Hexo的必要步骤以及简单排错

## 一. 准备工作:
### 1. 注册github帐号
    
## 二. 正式开始:

### **1. 安装github**
>   **下载地址**: [github]( https://git-for-windows.github.io) 
>   **安装**: 直接安装,下一步,下一步...

### **2. 安装node.js**
>   **下载地址**: [node.js下载](https://nodejs.org/en)
>   **安装**: 直接安装,下一步,下一步...
>   **验证**: `win+R`,输入`cmd`,输入`path`,查看node是否存在环境变量中,不存在时,想法添加(不讲解)

### **3. 安装Hexo**
>##### 3.1 `npm install -g hexo`
> &emsp;&emsp;**讲解**: // -g: 一般指全局有效, 否则只能相对于当前文件
>#####  3.2 `hexo init <folder>` 
> &emsp;&emsp;**讲解**: // folder为静态网站的目录
>#####  3.3 `hexo generate`
> &emsp;&emsp;**讲解**: // 生成静态网页
>#####  3.4 `hexo server`
> &emsp;&emsp;**讲解**: // 启动服务,通过localhost:4000进行查看
>#####  3.5 `hexo new 'title'`
> &emsp;&emsp;**讲解**: // 创建新网页,title为文章title

### **4. 上传到git**
>#### 4.1 基本配置
> 1. 配置 _config.yml
>    **讲解:** 
>   (1) 新增email  # github的email
        ```json
        email: xxxxx@邮箱.com
        ```
>   (2) 配置github账户和仓库
        ```json
        deploy:
            type: git
            repository: git@github.com:snaker95/snaker95.github.io
            branch: master
        ```
>   \* **注意:** 每个`:`后留下空格

> 2. 上传步骤:
    ```sh
    hexo generate
    hexo deploy
    ```     

> 3. 执行hexo deploy时,可能出现error:
        (1) no----.git 相关报错:
            &emsp;&emsp;**解决**: git init
        (2) no deployer ---.git
            &emsp;&emsp;**解决**: npm install hexo-deployer-git --save

>#### 4.2 更新步骤
>    1. `hexo clean`
>    2. `hexo generate`
>    3. `hexo deployer` 
           
----------

 **注意: 执行时注意顺序,不能打乱**

Author [@Snaker95][1]

[1]: http://www.sharedsea.com

