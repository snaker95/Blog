```bash
# vi /etc/docker/daemon.json
{
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
systemctl restart docker.service
```



```cpp
Docker中国区官方镜像
https://registry.docker-cn.com

网易
http://hub-mirror.c.163.com

ustc 
https://docker.mirrors.ustc.edu.cn

中国科技大学
https://docker.mirrors.ustc.edu.cn

阿里
https://mirror.aliyuncs.com
```

```shell
# 查看 docker 当前仓库
docker info|grep Mirrors -A 1
```



链接：https://www.jianshu.com/p/3b062c177508