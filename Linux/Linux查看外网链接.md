# Linux系统下查看外网正在链接

## 关键命令

* `netstat`
* netstat -anp | grep 正在链接 
* netstat -anp | grep ESTABLISHED | grep 22161| grep 3309

## 思路

通过`netstat -anp | grep ESTABLISHED` 查找所有的正在链接

通过grep 继续筛选