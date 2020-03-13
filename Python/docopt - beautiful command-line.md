---
title: docopt - beautiful command-line
categories: Python   
tags: Python
keywords: [Python]
description: Python docopt - beautiful command-line

---

[TOC]

# docopt - beautiful command-line
  在学习和使用python的过程中, 发现很多情况下python程序直接通过命令行启动和调用, 所以调用时传递参数和显示相关注释是灰常重要的, 这款docopt就灰常漂亮

## 基本讲解
### 主要使用方式
  * docopt 本质上是在 Python 中引入了一种针对命令行参数的形式语言，在代码的最开头使用"""文档注释的形式写出符合要求的文档，就会自动生成对应的parse，体验非常赞
  * 样例
  ```python
  import docopt
  def main():
    """Description
    Usage:
      scriptfilename [-vkh] [FILE] ...
      scriptfilename (--hard | --soft) CORRECTION FILE
    Arguments:
      FILE  optional input file
      CORRECTION correction angle, needs FILE, --hard or --soft to be present
    Options:
      -h --help show this
      -v  verbose mode
      -k  kill
      --hard hard
      --soft soft
      --speed=<km>  Speed in knots [default: 10]
    
    Examples:
      scriptfilename -v 123.md
    
    """
    args = docopt.docopt(main.__doc__)
    print(args)
  ```
  
### 主要参数
  * **Usage** 声明指令的调用方式, 不可缺少
    * **声明:** scriptfilename [-vkh] [FILE] ...
    * **调用:** python scriptfilename -v 123.txt 234.txt
    * **结果:** `{'--help: False', '-v': False, '-k': False, 'FILE': ['123.txt', '234.txt'], '--hard': False, '--soft': False, '--speed': '10'}`
    * **说明:**
    * 1. `scriptfilename` 为脚本名称
    * 2. `[-vkh]` 为调用参数
    * 3. `[]` 表示可选
    * 4. `...` 表示同一参数重复出现, 以数组形式输出
    * 5. `()` 表示必选参数
    
  * **Arguments** 对参数变量的说明
    * 例如`FILE`, `<km>`
    * 重点: 该参数变量使用`大写`或者`<>`来占位
  
  * **Options** 参数说明以及默认值指定
    * `[default: 10]` 指定默认值为10 
  
  * **Examples** 使用举例
  
### 参考:
  * [Git - docopt](https://github.com/docopt/docopt/tree/master/examples)
  
Author [@Snaker95][1]

[1]: http://www.sharedsea.com