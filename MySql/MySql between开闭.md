# MySql between 开闭情况

## 背景

在开发过程中, 很多时候使用 between.. and.. 进行区间筛选数据, 但是关于 between ... and .. 的区间开闭情况有时候会有些懵...., 生怕不用的数据库对这个函数的区间定义产生差异;

## 验证

* 极端验证

```mysql
# 输出 1 则为真, 标识在区间内
# 输出 0 则为假, 标识不在区间内
select 21 between 21 and 23; 
select 23 between 21 and 23;
```

## MySql

查看数据库版本 `select version();` => 5.7.20-log

between ... and ... 数据全闭

