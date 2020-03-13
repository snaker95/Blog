# 群晖Docker设置中国官方镜像加速

使用过群晖Docker的人都知道，Docker的镜像文件下载速度实在太慢了，所以经常造成下载失败。如今，Docker推出了中国官方镜像加速地址，来看看怎么设置吧！



一、打开Docker，注册表-设置-Docker Hub-编辑
[![1.jpg](https://wp.qiniu.gxnas.com/wp-content/uploads/2019/05/2029fb81cec128d73b4699162b2f17c5.jpg)](https://wp.qiniu.gxnas.com/wp-content/uploads/2019/05/2029fb81cec128d73b4699162b2f17c5.jpg)

 

二、启用注册表镜像打勾，在注册表镜像URL处填写：`https://registry.docker-cn.com`，确认退出。重启群晖。

[![2.jpg](https://wp.qiniu.gxnas.com/wp-content/uploads/2019/05/bb6ef74004eff87dc2083c030dc10571.jpg)](https://wp.qiniu.gxnas.com/wp-content/uploads/2019/05/bb6ef74004eff87dc2083c030dc10571.jpg)

三、你再下载一个Docker镜像试一下，感受一下快感吧！