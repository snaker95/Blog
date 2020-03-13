# shell 脚本学习

[TOC]

## 创建 shell 脚本的步骤

1. 创建 shell 文件, 比如, example.sh
2. 添加 shell 头部, 确定 shell 脚本的执行 shell
3. 添加可执行权限文件 `chmod +x exampl.sh`

## 头部

```shell
#!/bin/bash
```

标记 脚本执行时所需要的shell

## 变量

### 分类

* 临时变量: 在程序中中临时定义的
* 永久变量

### 注意事项

* 命名规则
* 使用变量值时, 需要前缀$
* 变量赋值时, =两边不能有空格
* 执行系统命令的方法
  * 反斜杠法 \`ls -l\` 
  * $大法 \$(ls -l)
* 变量拼接, 无连接符, 但尽量使用${变量}

### 查看所有系统变量 set 

### 删除系统变量 unset 变量

### 位置变量(位置参数)

* 通过$N 表达
* ./example.sh kkk er 中, $0 为 ./反斜杠法, \$1 为 kkk \$2 为 er

### 特殊变量

* $* 所有参数 将所有的参数作为一个整体
* $@ 会将各个参数分开，以换行形式输出所有参数
* $# 参数数量
* $$ 当前程序的 PID
* $! 执行上一个后台程序的 PID
* $? 执行上一次指令的返回值
* ![img](https://img-blog.csdnimg.cn/20190730150330987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTE5NDM3NA==,size_16,color_FFFFFF,t_70)



## 表达式 expr

a =\`expr 1+1\`

one=1

two=2

three=\`expr \$one + \$two`



## 变量测试 test

* test 升级 [  ]

  

### 字符串测试

* ==
* !=
* -n 字符串的值不为空，（相当于! -z）
* -z, 字符串的值为空   区别: -z的判断是通过判断字符串首位是否为\0来完成，而-n则变成了0

### 数字判断

* -eq：等于
  -ne：不等于
  -ge：大于或等于
  -le：小于或等于
  -gt：大于
  -lt：小于

### 文件测试

* 文件是否存在和大小
  * -e
  * -s

* 文件类型检测

  * -d 是否为目录

  * -f 是否为文件

  * -c 

  * -b 


* 文件权限判断
  * -x

  * -r

  * -w