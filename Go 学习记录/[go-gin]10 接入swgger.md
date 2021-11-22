# 接入 swagger

[TOC]

swagger 已经是标准的自动化生成接口文档的外部工具, 但是参数较多, 所以采用 goland中配置模板

### 配置 main.go 中标准的接口头

```json
// @title SnakeGin API
// @version 1.0
// @description 这是一个基于 gin搭建的框架.

// @contact.name Snaker95
// @contact.url
// @contact.email Snaker95@126.com

// @license.name XXXX
// @license.url XXXXX.com

// @host 127.0.0.1:8080
// @BasePath
```

### 配置 goland

* Preferences -> Editor -> Live Templates, 修改main模板

* ```json
  // @title $title$
  // @version $ver$
  // @description $desc$
  
  // @contact.name Snaker95
  // @contact.url $url$
  // @contact.email $mail$
  
  // @license.name $license$
  // @license.url $licenseUrl$
  
  // @host $host$
  // @BasePath $basePath$
  
  func main() {
   $END$
  }
  ```

### 配置Api接口方法

* Preferences -> Editor -> Live Templates, 添加swg模板

* ```json
  // @Summary $Summary$
  // @Description $Description$
  // @Tags $Tag$
  // @Accept $Accept$
  // @Produce $Produce$
  // @Param $param$ $from$ $dataType$ $bool$ "$desc$"
  // @Success 200 {object} $type$ "$example$"
  // @Failure 400 {object} $type$ "$example$"
  // @Router $path$ [$http$]
  ```

参考: https://razeencheng.com/post/go-swagger.html