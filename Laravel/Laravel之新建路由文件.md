---
title: Laravel之路由创建
categories: PHP
tags: Laravel
keywords: [PHP,Laravel,路由]
description: 创建不同的路由文件

---

[TOC]

# Laravel之路由创建
关于怎么书写路由以及其书写规则， 就不啰嗦了， 官方文档写的灰常清楚了！ 这里我讲下创建不同的路由文件！
## 情景： 
同一套Laravel框架下， 搭建API接口／Web网站／管理后台，由于Laravel框架已经自带了routes／web.php和routes/api.php, 所以现需要创建一个routes/admin.php的路由文件;

## 解决方案:
1. 拷贝routes/web.php,并重命名为admin.php
2. 在`提供服务者`中`路由服务提供`添加关于admin的路由; 
> 在app/Providers/RouteServiceProvider.php中, 复制一份 `function mapApiRoutes(){...}`, 并修改相应的`api`为`admin`
> 并在`map`方法中调用该方法` $this->mapAdminRoutes();`
3. 在`config/auth.php`中, 复制一份 并:修改如下
```php
    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
        'api' => [
            'driver' => 'token',
            'provider' => 'users',
        ],
        'admin' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
    ],
```
4. 由于使用到了`中间件`, 所以要在`app/Http/Kernel.php`中新增一组`中间件组`, 代码如下:
```php
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
        'api' => [
            'throttle:60,1',
            'bindings',
        ],
        'admin' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];
```

这样基本上就解决了当前情景, 但是, 可能会遇到其他情况, 导致无法成功.
## 解决其他问题
> 使用Route::prefix()不能成功的使用前缀: 最大的可能性是storage目录不具有可写权限, 此时:
```shell
$ sudo chmod -R 755 ./storage # 如果还不行, 换成777 尝试
```
> 除 `/`路由可以访问外其他路由不能访问, 这个可能是apache或者Nginx配置问题, 以Nginx为例:
```conf
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```
> 参考: https://docs.golaravel.com/docs/5.4/installation/#web-server-configuration

Author [@Snaker95][1]

[1]: http://www.sharedsea.com

