# 01 初始化项目

[TOC]

## 依赖


```shell
# go version go1.13.4
# 依赖包使用 go mod 加载
```



## 准备

* 创建目录 `mkdir ~/go/src/github.com/snkaker95/err-system`
* 初始化项目 `go mod init er-system`

## 目录结构

```shell
# tree ~/go/src/github.com/snkaker95/err-system
.
├── README.md
├── config # 配置文件, 以及加载配置
│   ├── config.go
│   ├── db.go
│   ├── dev.conf
│   ├── gin.go
│   ├── http.go
│   ├── logger.go
│   └── release.conf
├── docs # 接口文档
│   ├── docs.go
│   ├── swagger.json
│   └── swagger.yaml
├── go.mod
├── go.sum
├── logger # 日志
│   └── logger.go
├── logic # 逻辑层
│   ├── code_logic.go
│   ├── point_logic.go
│   └── project_logic.go
├── main.go # 入口文件
├── middleware # 中间件
│   ├── init.go
│   ├── logger.go
│   ├── params.go
│   ├── recovery.go
│   ├── request_id.go
│   └── sign_auth.go
├── model # model 层
│   ├── codes.go
│   ├── common.go
│   ├── points.go
│   └── projects.go
├── pkg # 封装的第三方包
│   ├── coderror
│   │   └── error.go
│   ├── compress
│   │   └── compress.go
│   └── map2struct
│       ├── map2struct.go
│       └── map2struct_test.go
└── router # 路由
    ├── api # api 接口
    │   ├── code.go
    │   ├── payloads # 参数数据结构
    │   │   ├── code_type.go
    │   │   ├── point_type.go
    │   │   ├── project_type.go
    │   │   └── requst.go
    │   ├── point.go
    │   └── project.go
    └── router.go # 路由文件
```

## 配置文件加载

思路:

*  使用 json 格式实现配置
*  通过使用 init-config.go 加载 json文件, 使用配置



## 中间件配置

* request-id 中间件
  * 请求中携带 x-request-id, 便于追踪查找一组日志记录
* 请求参数解析中间件
  * 将请求的参数, 尤其是 post 请求的参数, 设置到 context 中, 方便后续使用, 而不别要去判断 content-type 获取
* 日志中间件
  * 格式化日志输出, 同时将入参 request 和输出 response, 打印到日志中, 输出 response 采用压缩编码, 实现脱敏和节约空间
* 请求数据验签中间件 
  * 将验签数据放入 header 头

## 路由配置

* 分组模式, 实现不同的 api 接口, 方便维护和添加

  

## 验签参数获取

* 在中间件 `ParamsMiddleware` 中, 将request参数存放在 `c.Request.URL.Path` Key 下, 获取是使用: `c.Value(c.Request.URL.Path)`, 进行获取, 并进行验签

## 开发规范

* 配置文件
  * 尽量使用配置文件实现, 同时完善 init-config.go 中对新增配置的加载; 新增结构体 struct 和 对应的 getXXXConfig()方法
* 中间件
  * 针对特殊中间级, 可在 路由router中的分组中引入, 实现定制化
* 路由实现
  * 在 router.go 中增加, 采用 group 分组, 实现结构化, 方便维护和管理