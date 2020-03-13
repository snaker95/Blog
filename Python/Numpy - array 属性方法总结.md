---
title: Numpy - array 属性方法总结
categories: Python   
tags: Python
keywords: [Python]
description: Numpy - array 属性方法总结

---

[TOC]

# Numpy - array 属性方法总结
## 基本属性
  | 属性    | 描述    |
  | :----- | :----- |
  | a.dtype | 数组元素类型 float32, uint8 ...|
  | a.shape | 数组形状 (m, n, o ...) 元组类型|
  | a.size | 数组元素数 |
  | a.itemzise | 每元素占字节数 |
  | a.dbytes | 所有元素所占字节数 |
  | a.ndim | 数组纬度 |
  **备注:** 通过`a.shape`可以修改数组形状, 不过仅对原数组为一维数组支持性较好;
## 形状相关
  |   方法  |   描述  |
  | :----- | :----- |
  | a.flat | 返回所有元素的迭代器 |
  | a.flatten() | 返回一维数组, 不改变原数组|
  | a.ravel() | 返回一维数组, 不改变原数组, 高效|
  | a.resize(size) | 改变原数组形状, 比属性`shape`好使 |
  | a.swapaxes(ax1, ax2)| 交换两个维度的位置,返回新数组 |
  | a.transpose() | 交换所有纬度的位置, 返回新数组 |
  | a.T | a.transpose() 简洁版 |
  | a.squeeze() | 去除所有长度为1的维度, 返回新数组 |
  **备注:** 上述形状相关方法中, 除`a.resize(size)` 会改变原数组结构, 其他均不修改原数组
## 填充复制
  |   方法  | 描述  |
  | :----- | :----- |
  | a.fill(v) | 初始/设置数组每个元素为特定值, 以原数组的元素类型为基准自动转换|
  | a.copy() | 返回数组的复制对象 |
## 类型转换
  |   方法  | 描述  |
  | :----- | :----- |
  | a.tolist() | 将数组转化为列表, 返回列表, 不修改原数组 |
  | a.tostring() | 将数组转化为字节字符串, 返回字符串, 不修改原数组, 默认参数为order='C', 有'F', 'None' |
  | a.astype(dtype) | 转化为指定类型, 返回新类型数组 |
  | a.byteswap(Flase) | 转换大小字节序 |
  | a.view(type_or_dtype) | 生成一个使用相同内存，但使用不同的表示方法的数组 |
## 保存
  |   方法  | 描述  |
  | :----- | :----- |
  | a.dump(file) | 将二进制数据保存在file文件中 |
  | a.dumps() | 将二进制数据表示成字符串 |
  | a.tofile(fid, sep='', format='%s') | 格式化ASCII码写入文件 |
## 查找排序
  |   方法  | 描述  |
  | :----- | :----- |
  | a.nonzero() | 返回所有非零元素的索引, 可做花式索引 |
  | a.sort(axis=-1) | 沿某轴排序, 改变原数组 |
  | a.argsort(axis=-1) | 沿某个轴，返回按排序的索引 |
  | a.searchsorted(b) | 返回将b中元素插入a后能保持有序的索引值 |
## 数学操作
  |   方法  | 描述  |
  | :----- | :----- |
  | a.clip(low, high) | 将数值限制在一定范围内, 返回新数组 |
  | a.round(decimals=0) | 近似到指定精度, 返回新数组 |
  | a.cumsum(axis=None) | 累加和 |
  | a.cumprod(axis=None) | 累乘积 |
  | a.sum(axis=None) | 求和 |
  | a.prod(axis=None) | 求积 |
  | a.min(axis=None) | 最小值 |
  | a.max(axis=None) | 最大值 |
  | a.argmin(axis=None) | 最小值索引 |
  | a.argmax(axis=None) | 最大值索引 |
  | a.ptp(axis=None) | 最大值和最小值之差 |
  | a.mean(axis=None) | 平均值 |
  | a.std(axis=None) | 标准差 |
  | a.var(axis=None) | 方差 |
  | a.any(axis=None) | 只要有一个不为0，返回真，逻辑或 |
  | a.all(axis=None) | 所有都不为0，返回真，逻辑与 |
  
  