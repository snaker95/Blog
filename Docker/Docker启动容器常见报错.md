---
title: Docker启动容器常见错误
date: 2018-10-16 14:03:06
categories: [Docker]
tags: [Docker]
keywords: [docker, 容器]
description: 
---

`TOC`

# Docker启动容器常见错误



### 一. 重启docker服务器后 遇到 'device or resource busy'错误

```
Error response from daemon: Cannot start container ppchronos: Error getting container 2c00611c0f31701217e05a3d6dc31329664ed4b6b569a8252d395e433e1dd247 from driver devicemapper: Error mounting '/dev/mapper/docker-202:17-12107871-2c00611c0f31701217e05a3d6dc31329664ed4b6b569a8252d395e433e1dd247' on '/mnt/docker/devicemapper/mnt/2c00611c0f31701217e05a3d6dc31329664ed4b6b569a8252d395e433e1dd247': device or resource busy
Error: failed to start containers: [ppchronos]
```

**OR**

```
d2859bd1f84b: Error pulling image (latest) from xxxxxx, Driver devicemapper failed to create image rootfs e6158e7962db43274de40fc3db65ad64811d43fe342dea633df20639f5a4e3cd: device e6158e7962db43274de40fc3db65ad64811d43fe342dea633df20639f5a4e3cd already exists 43fe342dea633df20639f5a4e3cd already exists c049b2b: Download complete
e6158e7962db: Error downloading dependent layers
```



> 关键字**device or resource busy** 或 **Error downloading dependent layers**

* 解决方案: 
  * 找出没有umount的路径`cat /proc/mounts | grep "mapper/docker" | awk '{print $2}'`
  * 依次umount 已挂载的