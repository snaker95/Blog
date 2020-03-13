# 设计OAuth2 SSO 系统

## 参考

* 教程 :
  * https://blog.csdn.net/qiushisoftware/article/details/90906156
  * https://www.zifangsky.cn/1327.html
* 代码demo:
  * https://gitee.com/zifangsky/OAuth2.0Demo/tree/master/ServerDemo
* 数据库设计
  * https://gitee.com/zifangsky/OAuth2.0Demo/blob/master/rbac_db.sql

* docker 国内镜像
  * https://registry.docker-cn.com



## 设计

### * 数据库设计

```mysql
# Dump of table oauth_access_token
# ------------------------------------------------------------

CREATE TABLE `oauth_access_token` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `access_token` varchar(255) NOT NULL COMMENT 'Access Token',
  `user_id` varchar(100) NOT NULL DEFAULT '' COMMENT '关联的user的user_id',
  `client_id` int(11) NOT NULL COMMENT '接入的客户端ID',
  `expires_in` bigint(20) NOT NULL COMMENT '过期时间戳',
  `grant_type` varchar(50) DEFAULT '' COMMENT '授权类型，比如：authorization_code',
  `scope` varchar(255) DEFAULT '' COMMENT '可被访问的用户的权限范围，比如：basic、super',
  `create_at` datetime NOT NULL DEFAULT current_timestamp() COMMENT '创建时间',
  `update_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '最后更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_access_token` (`access_token`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='Access Token信息表';



# Dump of table oauth_authorization_codes
# ------------------------------------------------------------

CREATE TABLE `oauth_authorization_codes` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `authorization_code` varchar(255) DEFAULT '' COMMENT '授权码',
  `client_id` varchar(100) DEFAULT '' COMMENT '接入客户端的client_id',
  `user_id` varchar(100) DEFAULT '0' COMMENT '用户user_id',
  `redirect_uri` varchar(2000) DEFAULT '' COMMENT '跳转认证的uri',
  `expires` bigint(20) NOT NULL DEFAULT 0 COMMENT '有效时长 单位s',
  `scope` varchar(255) DEFAULT NULL COMMENT '可被访问的用户的权限范围，比如：basic、super',
  `create_at` datetime NOT NULL DEFAULT current_timestamp(),
  `update_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_code` (`authorization_code`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='授权码';



# Dump of table oauth_client
# ------------------------------------------------------------

CREATE TABLE `oauth_client` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `client_id` varchar(100) NOT NULL COMMENT '接入的客户端ID',
  `client_name` varchar(100) DEFAULT NULL COMMENT '接入的客户端名称',
  `client_secret` varchar(255) NOT NULL COMMENT '接入的客户端的密钥',
  `redirect_uri` varchar(1000) NOT NULL COMMENT '回调地址',
  `description` varchar(1000) DEFAULT NULL COMMENT '描述信息',
  `state` tinyint(1) NOT NULL DEFAULT 0 COMMENT '当前状态. 0: 正常 -1: 禁用',
  `create_at` datetime NOT NULL DEFAULT current_timestamp() COMMENT '创建时间',
  `update_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '最后更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_client_id` (`client_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='接入的客户端信息表';

LOCK TABLES `oauth_client` WRITE;
/*!40000 ALTER TABLE `oauth_client` DISABLE KEYS */;

INSERT INTO `oauth_client` (`id`, `client_id`, `client_name`, `client_secret`, `redirect_uri`, `description`, `state`, `create_at`, `update_at`)
VALUES
	(1,'x3qwrgrO1wYdz72joZ8YyIuD','测试客户端','DPTyJsBv1xjzEgv0MEjBnIVigaVUm3cr','http://127.0.0.1:7000/user/userIndex','这是一个测试客户端服务',-1,'2018-08-06 15:17:18','2019-12-03 07:09:36'),
	(2,'7Ugj6XWmTDpyYp8M8njG3hqx','zifangsky的个人博客','tur2rlFfywR9OOP3fB5ZbsLTnNuNabI3','http://localhost:7080/login','用于测试在客户端应用中连接授权服务器',0,'2018-08-23 11:30:09','2018-08-23 11:30:09'),
	(3,'pbAjLKuogxziSF2hGlRdl066','测试客户端2','xUkqZSQQgY5SqfD9qqYXCquWRpiHTbTp','http://127.0.0.1:7000/user/userIndex','这是一个测试客户端服务',0,'2018-08-31 16:39:52','2018-08-31 16:39:52');

/*!40000 ALTER TABLE `oauth_client` ENABLE KEYS */;
UNLOCK TABLES;


# Dump of table oauth_refresh_token
# ------------------------------------------------------------

CREATE TABLE `oauth_refresh_token` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `token_id` int(11) NOT NULL COMMENT '表auth_access_token对应的Access Token记录',
  `refresh_token` varchar(255) NOT NULL COMMENT 'Refresh Token',
  `expires_in` bigint(20) NOT NULL COMMENT '过期时间戳',
  `create_at` datetime NOT NULL DEFAULT current_timestamp() COMMENT '创建时间',
  `update_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '最后更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_refresh_token` (`refresh_token`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='Refresh Token信息表';

LOCK TABLES `oauth_refresh_token` WRITE;
/*!40000 ALTER TABLE `oauth_refresh_token` DISABLE KEYS */;

INSERT INTO `oauth_refresh_token` (`id`, `token_id`, `refresh_token`, `expires_in`, `create_at`, `update_at`)
VALUES
	(1,1,'2.b19923a01cf35ccab48ddbd687750408bd1cb763.31536000.1566544316',1566544316,'2018-08-20 14:50:27','2018-08-23 15:11:57'),
	(2,2,'2.cb8a3e903eecd6b74fc33c111b4a286099ace3ce.31536000.1566544978',1566544978,'2018-08-23 14:08:07','2018-08-23 15:22:59');

/*!40000 ALTER TABLE `oauth_refresh_token` ENABLE KEYS */;
UNLOCK TABLES;


# Dump of table oauth_scope
# ------------------------------------------------------------

CREATE TABLE `oauth_scope` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `scope` varchar(255) NOT NULL DEFAULT '' COMMENT '可被访问的用户的权限范围，比如：basic、super',
  `description` varchar(100) DEFAULT NULL COMMENT '对scope的描述',
  `state` tinyint(1) NOT NULL DEFAULT 0 COMMENT '状态: 0: 正常 -1: 禁用',
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_scope` (`scope`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='可被访问的用户权限表';

LOCK TABLES `oauth_scope` WRITE;
/*!40000 ALTER TABLE `oauth_scope` DISABLE KEYS */;

INSERT INTO `oauth_scope` (`id`, `scope`, `description`, `state`)
VALUES
	(1,'super','用户所有信息',0),
	(2,'basic','用户基本信息',0);

/*!40000 ALTER TABLE `oauth_scope` ENABLE KEYS */;
UNLOCK TABLES;


# Dump of table user
# ------------------------------------------------------------

CREATE TABLE `user` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `user_id` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT 'UUID' COMMENT '展示层用户的唯一表示uuid()',
  `phone_encrypt` varchar(64) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '登录手机号',
  `email` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '用户邮箱',
  `emergency_phone_encrypt` varchar(64) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '紧急手机号',
  `displayname` varchar(50) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '前端展示名称',
  `register_from` varchar(16) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT 'web' COMMENT '用户注册来源',
  `register_channel` varchar(30) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT 'pp' COMMENT '用户注册渠道',
  `state` tinyint(1) NOT NULL DEFAULT 0 COMMENT '用户当前的状态, -1: 无效 0: 未激活, 1: 已激活',
  `created_at` datetime NOT NULL DEFAULT current_timestamp() COMMENT '注册时间',
  `updated_at` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_key` (`user_id`),
  UNIQUE KEY `u_email` (`email`),
  UNIQUE KEY `u_phone_encrypt` (`phone_encrypt`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='用户注册表';
```

