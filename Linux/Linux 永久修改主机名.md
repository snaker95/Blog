# Linux 永久修改主机名 hostname

[TOC]

Linux 系统安装完成后, 都会是默认主机名, 或者在阿里云上使用的是实例名称, 所以, 希望能永久修改主机名为规划的

## 临时修改

```shell
hostname XXX
```

重启服务器后将会失效

## 永久修改

* 修改主机名 `vim /etc/sysconf/network`

* ```conf
  # 新增或者修改
  HOSTNAME=XXX
  ```

* 修改 hosts 解析 `vim /etc/hosts`

* ```conf
  # 新增
  127.0.0.1 XXX
  ```

  永久修改需要重启服务器, 如果暂时不想重启, 需要配置**临时修改**方案

* 或者使用命令 `hostnamectl set-hostname 主机名`