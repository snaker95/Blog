

## 配置免密 ssh 登录

```shell
# 配置 ssh, 粘贴公钥
vim ~/.ssh/authorized_keys
# 关闭用户名密码登录
vim /etc/ssh/sshd_config
' PasswordAuthentication no
' ChallengeResponseAuthentication no
```

## 修改 hostname

```shell
vim /etc/sysconfig/network
' HOSTNAME=snaker-95
vim /etc/hosts
' 127.0.0.1 snaker-95
```

## Mysql 配置

```shell
docker run --name MySQL --restart=on-failure:10 -p 3307:3306 -v /data/MySQL:/var/lib/mysql: -e MYSQL_ROOT_PASSWORD='Sn@ker95.' -d mysql:latest --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

docker ps
docker exec -it 29dd792569bf /bin/bash
mysql -h127.0.0.1 -uroot -pSn@ker95.
# 关闭 root 用户远程登录
use mysql
update user set Host = '127.0.0.1' where User ='root' and Host ='%';
# 创建其他名称的超级用户	
create user 'snaker95'@'%' identified by 'Sn@ker95.';
grant all privileges on *.* to 'snaker95'@'%';
# 创建只读用户
create user 'readonly'@'%' identified by 'Re@d0nly.';
grant select on *.* to 'readonly'@'%' with grant option;
# 创建客户端使用
create user 'snaker95-native'@'%' identified with mysql_native_password by 'Sn@ker95.';
grant all privileges on *.* to 'snaker95-native'@'%';

```

## MongoDB配置

```shell
docker run --name Mongo --restart=on-failure:10 -p 27017:27017 -v /data/MongoDB/db:/data/db -d mongo

docker ps
docker exec -it 29dd792569bf /bin/bash
mongo

db.createUser({ user: 'snaker95', pwd: 'Sn@ker95.', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] })
# 拥有对数据库app的读写权限。
use app
db.createUser(
  {
    user: "swen",
    pwd: "swen",
    roles: [ { role: "readWrite", db: "app" }
             ]
  }
)
```

## Redis 配置

```shell
docker run --name Redis --restart=on-failure:10 -p 6379:6379 -v /data/Redis:/data -d redis redis-server --appendonly yes --requirepass "R3dis."
# R3dis.
```

## JupyterBook

```shell
docker run --name jupyter-notebok -p 8888:8888  --restart=on-failure:10 -v /notebook:/go/src/JupyterBook -d gopherdata/gophernotes:latest-ds

-- token=f7fcf8a809a07159b89c9bade482b3b50254b92dddb0d58a
```

## 安装cloud9

```shell
cd /cloud9/workspace
docker run -d -v $(pwd):/workspace -p 8181:8181 sapk/cloud9:golang-alpine --auth snaker95:snaker95.
```

## 安装 gitlab

```shell
# 拉取镜像
docker pull gitlab/gitlab-ce:rc

# 启动 docker
docker run -d --name gitlab --restart always \
    -p 9443:443 -p 9080:80 -p 9022:22 \
    -v /docker-img-config/gitlab/conf:/etc/gitlab \
    -v /docker-var-log/gitlab/logs:/var/log/gitlab \
    -v /data:/var/opt/gitlab \
    gitlab/gitlab-ce:rc
```

>  注意：不要把容器的 `22` 端口映射到宿主机的 `22`，宿主机的该端口已经开启了 `sshd` 进程的监听，用于客户端的 `ssh` 远程登录。

