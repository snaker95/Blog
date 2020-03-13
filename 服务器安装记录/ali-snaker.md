

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

# 关闭 root 用户远程登录
update user set Host = '10.%' where User ='root' and Host ='%';
# 创建其他名称的超级用户	
create user 'snaker95'@'%' identified by 'Sn@ker95.';
grant all privileges on *.* to 'snaekr95'@'%';
# 创建只读用户
create user 'readonly'@'%' idnentified by 'Re@d0nly.';
grant select on *.* to 'readonly'@'%' with grant option;
# 创建客户端使用
create user 'snaker95-native'@'%' identified with mysql_native_password by 'Sn@ker95.';
grant all privileges on *.* to 'snaekr95-native'@'%';

```

## MongoDB配置

```shell
docker run --name Mongo --restart=on-failure:10 -p 27017:27017 -v /data/MongoDB/db:/data/db -d mongo

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

