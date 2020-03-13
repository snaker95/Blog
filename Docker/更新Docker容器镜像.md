# 如何更新docker容器镜像

docker镜像出了新版本，但是当前运行的容器还是老版的，遂尝试更新容器镜像

方法

1. 拉取最新镜像
2. 停止并删除当前运行容器
3. 用最新镜像重新创建容器
4. All Done！

### 拉取最新镜像

查找当前系统中使用的镜像



| 1    | docker images |
| ---- | ------------- |
|      |               |

拉取最新镜像



| 1    | docker pull jpillora/cloud-torrent |
| ---- | ---------------------------------- |
|      |                                    |

输出显示



| 123456 | Using default tag: latestlatest: Pulling from jpillora/cloud-torrent1eae7a7426b0: Pull complete 4136a8c2b2a4: Pull complete Digest: sha256:2ae384f5f4b539b274e08458dae63296e9524aa1a82a1db01051933ff6e7f4e3Status: Downloaded newer image for jpillora/cloud-torrent:latest |
| ------ | ------------------------------------------------------------ |
|        |                                                              |



### 停止并删除当前运行容器

查找容器ID



| 1    | docker ps |
| ---- | --------- |
|      |           |

导出之前的容器配置信息



| 1    | docker inspect 8992319a1585 |
| ---- | --------------------------- |
|      |                             |

8992319a1585是通过docker ps获取的容器ID，在输出的json中的Args和HostConfig属性里可以找到容器当初的启动参数。

停止容器



| 1    | docker kill 8992319a1585 |
| ---- | ------------------------ |
|      |                          |

删除容器



| 1    | docker rm 8992319a1585 |
| ---- | ---------------------- |
|      |                        |



### 用最新镜像重新创建容器





| 1    | docker run --name ct -d -p 63000:63000 --restart always -v /root/downloads:/downloads jpillora/cloud-torrent --port 63000 --auth user:pass |
| ---- | ------------------------------------------------------------ |
|      |                                                              |



### All Done！