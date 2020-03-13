---
title: Numpy - array以及索引
categories: Python   
tags: Python
keywords: [Python]
description: Numpy - array以及索引

---

[TOC]

# Numpy - array以及索引
## 安装和引入Numpy模块
  * 安装 `pip install numpy`
  * 引入 `import numpy as np`
## 数组 array
  * 生成数组
    ```python
    lst = [1, 2, 3, 5]
    a = np.array(lst)
    # array([1, 2, 3, 5])
    ```
  * 数组属性
    * 查看类型 `type`
      ```python
      type(a)
      # numpy.ndarray
      ```
    * 查看数组中的数据类型 `dtype`
      ```python
      a.dtype
      # dtype(int32)
      ```
    * 查看每元素所占的字节 `itemsize`
      ```python
      a.itemsize
      # 4
      ```
    * 查看数组形状 `shape`
      ```python
      a.shape
      # (4L,)
      ```
    * 查看元素数目 `size`
      ```python
      a.size
      # 4
      ```
    * 查看所有元素所占的空间 `nbytes`
      ```python
      a.nbytes
      # 16 # 备注: 实际要大于这个值
      ```
    * 查看数组的维数 `ndim`
      ```python
      a.ndim
      # 1
      ```
  * 设置初始值 `fill`
    ```python
    a.fill(-4.8)
    # array([-4, -4, -4, -4]) 
    # 备注: 如果传入参数的类型与数组类型不一样，需要按照已有的类型进行转换
    ```
  * 数组索引和切片
    * 一维数组`索引`和`切片`
      * 与列表类似, 不做详细记录
      * 重点: 切片在内存中使用的是引用机制, 修改切片后的值将会改变原数组的值
    * 多维数组`索引`
      * `a[n, m]` 或者 `a[(n, m)]`, 其实二者是等同的, n表示行, m表示列
      * `a[n]`, 将获取整行, 返回类型仍为ndarray
    * 多维数组`切片`
      * 多维数组切片和一维数组切片类似, 同样是`lower:upper:step`, 只不过是多维数组索引书写方式
      * 同样, 切片是引用
    * 切片时不适用引用 `copy()`
      ```python
      b = a[2:4].copy()
      ```
  * 数组的花式索引
    * 定义: 在索引位置使用列表类型或者布尔数组进行索引
    * 列表类型: 
      ```python
      # 假设a为3维数组
      indices = [1, 2, -1]
      y = a[:, : indices]
      ```
    * 布尔数组:
      ```python
      # 假设a为3维数组
      mask = np.array([0, 1, 0, 1, 0], dtype=bool)
      y = a[:, mask, :]
      ```
    * 注释: 花式索引是原对象的复制, 而不是引用
  
  * 数组的比较运算和`np.where`
    * 比较运算: 返回布尔数组, N维 对 N维
    * `where`: 返回元组类型, 元素为数组类型, 长度为原数组N, 元素中的值为所在行列的索引
    ```python
    a = np.array([[1,2,4],
                  [4,5,6]
                  [7,8,9]])
    a > 5
    # array([[False,False,False],
    #       [False,False,True]
    #       [True,True,True]], dtype=bool)
    
    np.where(a > 5)
    # (array([1, 2, 2, 2], dtype=int64),
    #  array([2, 0, 1, 2], dtype=int64))
    ```
  * 参考
    [Git-notes-python](https://github.com/lijin-THU/notes-python/tree/master/03-numpy)

  Author [@Snaker95][1]

  [1]: http://www.sharedsea.com