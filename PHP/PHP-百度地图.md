---
title: PHP 请求百度地图
categories: PHP
tags: PHP
keywords: [PHP, 百度地图]
description: 在很多时候, 网站后台需要通过百度地图接口获取相应的地理经纬度或者其他一些和地图相关的需求

---

<!--more-->

[TOC]

# PHP 请求百度地图
在很多时候, 网站后台需要通过百度地图接口获取相应的地理经纬度或者其他一些和地图相关的需求, 但是, 存在一种很奇怪的现象(项目中遇到的), 在不经意间请求百度地图接口超时问题, 导致获取的数据不对甚至没有数据返回!在此, 为解决问题而完成.
## 1. 使用CURL 和 file_get_contents
通过`度娘`和`谷爹`查找, 大神们和大婶儿们都是`CURL`的效率比`file_get_contents`效率要高, 鄙人也就这么认为了(主要是不知道怎么有效的测试).

## 2. 封装 `CURL`  
详见: <a href="/PHP/PHP/PHP%20Curl请求数据/" target="_blank">[PHP Curl请求数据]</a>

## 3. 请求百度地图接口

```php
/**
 * 获取单个地区详情by经纬度
 * @param $data array
 * @return mixed
 */
function get_info_by_lat($lat, $lon, $i=0) {
    try{
        $url = 'http://api.map.baidu.com/geocoder/v2/?ak=B48de2d97210ccaa64d20a2eb69c16dd&location=';
        $url .= $lat.','.$lon.'&output=json';
        $baidu_info = httpGet($url);
        $baidu_info = json_decode($baidu_info,true);
        if( $baidu_info && $baidu_info['result']['cityCode']>0){
            $baidu_code = $baidu_info['result']['cityCode'];
            $retval = $this -> db -> fetch_first("select * from city where baidu_code = ".$baidu_code." limit 1");
            return $retval;
        }
    }catch(Exception $e){

    }
    if($i < 10) {
        $i++;
        return get_info_by_lat($lat, $lon, $i);
    }else{
        return false;
    }
}

/**
 * 获取城市经纬度
 * @param $address string
 * @return mixed
 */
function get_address_lat($address,$city='', $i=0){
    try{
        $address = trimall($address);
        $city = trimall($city);
        $url = "http://api.map.baidu.com/geocoder/v2/";
        $url .= "?ak=B48de2d97210ccaa64d20a2eb69c16dd&output=json&address={$address}&city={$city}";
        $json = httpGet($url);
        $data = json_decode($json,true);
        if($data['status'] == 0){
            return $data['result']['location'];
        }
    }catch(Exception $e){

    }
    // 不成功 循环10次
    if($i < 10) {
        $i++;
        return get_address_lat($address, $city, $i);
    }
    return false;
}

function trimall($str)//删除空格
{
    $qian=array(" ","　","\t","\n","\r");$hou=array("","","","","");
    return str_replace($qian,$hou,$str);
}
```

**备注: 使用递归方法请求百度地图接口, 降低请求失败的机率**
Author [@Snaker95][1]

[1]: http://www.sharedsea.com