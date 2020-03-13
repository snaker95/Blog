---
title: PHP 自动加载类
categories: PHP
tags: PHP
keywords: [PHP, 加载类]
description: 在写php项目不是用框架的时候, 很多时候需要解决引入类的问题, 简单使用include/require/include_once/require_once, 后期会有大量的依赖问题, 或者重复引入类

---

<!--more-->

[TOC]
# PHP 自动加载类
  在写php项目不是用框架的时候, 很多时候需要解决引入类的问题, 简单使用include/require/include_once/require_once, 后期会有大量的依赖问题, 或者重复引入类, 在此, 结合PHP官方文档写下此类, 方便与人方便与己!
 
## 代码部分 
```php
namespace loader;

class autoloader {

    const APP_PATH = '.';

    public static $loader;

    public static function init()
    {
        if (self::$loader == NULL)
            self::$loader = new self();

        return self::$loader;
    }

    public function __construct()
    {
        spl_autoload_register(array($this,'controller'));
        spl_autoload_register(array($this,'model'));
        spl_autoload_register(array($this,'helper'));
        spl_autoload_register(array($this,'library'));
    }

    public function library($library, $instance=false)
    {
        if (is_string($library)) {
            $this->__library($library);
        }
        if (is_array($library)) {
            foreach ($library as $key) {
                $this->__library($key);
            }
        }
        if(false!==$instance) return $this->instance($library);

    }

   private function __library($class){
        set_include_path($this::APP_PATH.'/lib/');
        spl_autoload_extensions('.library.php');
        spl_autoload($class);
    }

    public function controller($class, $instance=false)
    {
        $class = preg_replace('/_controller$/ui','',$class);

        set_include_path($this::APP_PATH.'/controller/');
        spl_autoload_extensions('.controller.php');
        spl_autoload($class);

        if(false!==$instance) return $this->instance($class, 'controller');
    }

    public function model($class, $instance=false)
    {
        $class = preg_replace('/_model$/ui','',$class);
        set_include_path($this::APP_PATH.'/model/');
        spl_autoload_extensions('.model.php');
        spl_autoload($class);
        if(false!==$instance) return $this->instance($class, 'model');
    }

    public function helper($class)
    {
        $class = preg_replace('/_helper$/ui','',$class);

        set_include_path($this::APP_PATH.'/helper/');
        spl_autoload_extensions('.helper.php');
        spl_autoload($class);
    }

    private function instance($class, $namespace='')
    {
        try {
            if (is_array($class)) {
                $arrayObject = [];
                foreach($class as $one) {
                    var_dump($one);
                    $new = $namespace.'\\'.$one;
                    $arrayObject[] =  new $new;
                }
                return $arrayObject;
            }
            if (is_string($class)) {
                $new = $namespace.'\\'.$class;
                return new $new;
            }else {
                throw new \Exception('Class name must be string.');
            }
            if (null == $class) {
                throw new \Exception('You must enter the name of the class.');
            }
        } catch(Exception $exception) {
            echo $exception;
        }
    }

}

//call
autoloader::init();
```

## 单元测试
```php
    use loader\autoloader as autoloader;
    require 'autoloader.php'; // 请注意目录结构

    $autoloader = new autoloader();
    // 引入并实例化
    list($user_b, $group_b) = $autoloader->library(['User','Group'], true);
    $user_c = $autoloader->controller('User', true);
    var_dump($user_b);
    var_dump($group_b);
    var_dump($user_c);
    var_dump($user_c->get());

    //  or
    //  仅仅引入, 但是如果有命名空间会存在问题
    $autoloader->controller('User');
    $user_c = new User();
    $autoloader->helper('functions');
    echo abc();

    // or
    // 自动加载, 存在命名空间是必须有命名空间+类名
    try{
        $user = new controller\User();
        var_dump($user);
    }catch(Exception $e){
        echo $e;
    }
    var_dump($user->get());

```

## 目录结构
```
|-- controller
    |-- *.controller.php
|-- model
    |-- *model.php
|-- library
    |-- *.lirary.php
|-- helper
    |-- *.helper.php
|-- autoloader.php
|-- test.php
```
Author [@Snaker95][1]

[1]: http://www.sharedsea.com