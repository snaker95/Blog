---
title: PHP Curl请求数据
categories: PHP
tags: PHP
keywords: [PHP,CURL]
description: PHP已是公认世界上最好的语言, PHP中的CURL扩展帮助猿类们实现后台请求其他接口, 或者请求网络中其他的数据, 此时, 封装CURL成为一个函数方法, 简化在使用中的复杂程度

---

[TOC]

# PHP Curl请求数据
PHP已是公认世界上最好的语言, 最主要是俺是PHP程序猿, PHP中的CURL扩展帮助猿类们实现后台请求其他接口, 或者请求网络中其他的数据, 此时, 封装CURL成为一个函数方法, 简化在使用中的复杂程度,
<!--more-->
## 一. 最简单的`GET`请求

```php
function httpGet($url){
    $oCurl = curl_init();
    if(stripos($url,"https://")!==FALSE){
        curl_setopt($oCurl, CURLOPT_SSL_VERIFYPEER, FALSE);
        curl_setopt($oCurl, CURLOPT_SSL_VERIFYHOST, FALSE);
        curl_setopt($oCurl, CURLOPT_SSLVERSION, 1); //CURL_SSLVERSION_TLSv1
    }
    curl_setopt($oCurl, CURLOPT_URL, $url);
    curl_setopt($oCurl, CURLOPT_RETURNTRANSFER, 1 );
    $sContent = curl_exec($oCurl);
    $aStatus = curl_getinfo($oCurl);
    curl_close($oCurl);
    if(intval($aStatus["http_code"])==200){
        return $sContent;
    }else{
        return false;
    }
}
```

**注:** 此方法可支持`http`和`https`协议

## 二. 封装成类

```php
class Curl {
    public $header = false;
    /**
     *
     * @param type $method 请求方式
     * @param type $url 地址
     * @param type $fields 附带参数，可以是数组，也可以是字符串
     * @param type $userAgent 浏览器UA
     * @param type $httpHeaders header头部，数组形式
     * @param type $username 用户名
     * @param type $password 密码
     * @return boolean
     */
    public  function execute(
        $method,
        $url,
        $fields = '',
        $userAgent = '',
        $httpHeaders = '',
        $username = '',
        $password = ''
        ) {

        $ch = $this->create();
        if (false === $ch) {
            return false;
        }
        if (is_string($url) && strlen($url)) {
            $ret = curl_setopt($ch, CURLOPT_URL, $url);
        } else {
            return false;
        }
        //是否显示头部信息
        curl_setopt($ch, CURLOPT_HEADER, $this->header);
        //
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        if ($username != '') {
            curl_setopt($ch, CURLOPT_USERPWD, $username . ':' . $password);
        }

        if (stripos($url, "https://") !== FALSE) {
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
        }

        $method = strtolower($method);
        if ('post' == $method) {
            curl_setopt($ch, CURLOPT_POST, true);
            if (is_array($fields)) {
                $sets = array();
                foreach ($fields AS $key => $val) {
                    $sets[] = $key . '=' . urlencode($val);
                }
                $fields = implode('&', $sets);
            }
            curl_setopt($ch, CURLOPT_POSTFIELDS, $fields);
        } else if ('put' == $method) {
            curl_setopt($ch, CURLOPT_PUT, true);
        }
        //curl_setopt($ch, CURLOPT_PROGRESS, true);
        //curl_setopt($ch, CURLOPT_VERBOSE, true);
        //curl_setopt($ch, CURLOPT_MUTE, false);
        curl_setopt($ch, CURLOPT_TIMEOUT, 20); //设置curl超时秒数
        if (strlen($userAgent)) {
            curl_setopt($ch, CURLOPT_USERAGENT, $userAgent);
        }
        if (is_array($httpHeaders)) {
            curl_setopt($ch, CURLOPT_HTTPHEADER, $httpHeaders);
        }
        $ret = curl_exec($ch);
        if (curl_errno($ch)) {
            curl_close($ch);
            return array(curl_error($ch), curl_errno($ch));
        } else {
            curl_close($ch);
            if (!is_string($ret) || !strlen($ret)) {
                return false;
            }
            return $ret;
        }
    }

    /**
     * 发送POST请求
     * @param type $url 地址
     * @param type $fields 附带参数，可以是数组，也可以是字符串
     * @param type $userAgent 浏览器UA
     * @param type $httpHeaders header头部，数组形式
     * @param type $username 用户名
     * @param type $password 密码
     * @return boolean
     */
    public function post(
        $url, 
        $fields, 
        $userAgent = '', 
        $httpHeaders = '', 
        $username = '', 
        $password = ''
        ) {
        $ret = $this->execute(
            'POST', 
            $url, 
            $fields, 
            $userAgent, 
            $httpHeaders, 
            $username, 
            $password
            );
        if (false === $ret) {
            return false;
        }
        if (is_array($ret)) {
            return false;
        }
        return $ret;
    }

    /**
     * GET
     * @param type $url 地址
     * @param type $userAgent 浏览器UA
     * @param type $httpHeaders header头部，数组形式
     * @param type $username 用户名
     * @param type $password 密码
     * @return boolean
     */
    public function get(
        $url, 
        $userAgent = '', 
        $httpHeaders = '', 
        $username = '', 
        $password = ''
        ) {
        $ret = $this->execute(
            'GET', 
            $url, 
            "", 
            $userAgent, 
            $httpHeaders, 
            $username, 
            $password
            );
        if (false === $ret) {
            return false;
        }
        if (is_array($ret)) {
            return false;
        }
        return $ret;
    }

    /**
     * curl支持 检测
     * @return boolean
     */
    public function create() {
        $ch = null;
        if (!function_exists('curl_init')) {
            return false;
        }
        $ch = curl_init();
        if (!is_resource($ch)) {
            return false;
        }
        return $ch;
    }

}
```

> *&#42; 这是引用大牛的*

## 总结
> 只为分享, 别为不的

Author [@Snaker95][1]

[1]: http://www.sharedsea.com