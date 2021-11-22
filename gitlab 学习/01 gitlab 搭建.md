# 01 gitlab 搭建

## 学习文档

https://www.bookstack.cn/read/gitlab-doc-zh/docs-002.md

## 安装

* 要求

  * 最低配置 2 核 4G

* docker 安装

  * ```shell
    sudo docker run --detach \
      --hostname gitlab.mczaiyun.top \
      --publish 443:443 --publish 80:80 --publish 222:22 \
      --name gitlab \
      --restart always \
      --volume /volume1/docker/gitlab/config:/etc/gitlab \
      --volume /volume1/docker/gitlab/logs:/var/log/gitlab \
      --volume /volume1/docker/gitlab/data:/var/opt/gitlab \
      gitlab/gitlab-ce:latest
    ```

