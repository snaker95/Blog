---
title: PHP 前后台交互类
categories: PHP
tags: PHP
keywords: [PHP,交互]
description: 在WEB项目中, 前后台交互具有统一的格式会使很多工作很容易做好, 同时也可以做到一些功能模块化

---
# PHP 前后台交互类
在WEB项目中, 前后台交互具有统一的格式会使很多工作很容易做好, 同时也可以做到一些功能模块化; 所以这里自己参考大牛们搬写了一个前后台交互的类
<!--more-->
## 代码

```php
/**
 * 命令返回类封装
 * @author snaker95
 */
class ReturnPacket {
    /**
     * 命令返回状态 400表示处理失败,200表示处理成功,-9表示用户session丢失;
     * @var int
     */
    public $status = 400;

    /**
     * 命令返回消息
     * @var string
     */
    public $msg = '';

    /**
     * 命令返回的统计总条数,此属性用于分页显示总记录条数
     * @var int
     */
    public $count;

    /**
     * 数据，一般是array
     * @var array
     */
    public $data = array();

    /**
     * 每页显示条数,此属性非命令返回，后续添加,用户分页处理
     * @var int
     */
    public $perPageSize = 6;

    /**
     * 总页码数,此属性非命令返回，后续添加
     * @var int
     */
    public $totalPage = 1;

    /**
     * 当前页码
     * @var int
     */
    public $currentPage = 1;

    /**
     * 显示页码数,默认显示5个页码
     * @var int
     */
    private $viewCount = 5;

    /**
     * 当前页面显示的页码,为一个数组
     * @var array
     */
    public $viewNumbers;

    /**
     * 获取可返回前一次页面的url
     * @var string
     */
    public $url;

    /**
     * 構造函數
     */
    public function __construct(
             $count = 0, 
             $currentPage = 1,
             $perPageSize = 10, 
             $viewCount = 5 
    ) {
        // 初始化数据
        $this->status = 400;
        $this->msg = '';
        $this->url = '';

        $this->count = $count;
        $this->perPageSize = $perPageSize;
        $this->currentPage = $currentPage;
        $this->totalPage = 1;
        $this->viewCount = $viewCount;
        $this->viewNumbers = array ();

        // 计算数据
        $this->getTotalPage(); // 计算总页码
        $this->viewPageNumbers(); // 显示页面
        $this->_get_back_url(); // 设置可返回的url
    }

    /**
     * 获取总页码
     */
    public function getTotalPage() {
       if ($this->count > 0) {
         if (fmod ( $this->count, $this->perPageSize ) == 0) {
          $this->totalPage = $this->count / $this->perPageSize;
         } else {
          $this->totalPage = intval ( $this->count / $this->perPageSize ) + 1;
         }
       } else {
         $this->totalPage = 1;
       }
    }

    /**
     * 当前要显示的页码
     */
    public function viewPageNumbers() {
       // 若总页码小于等于默认显示页码,则全部显示
       if ($this->totalPage <= $this->viewCount) {
         for($i = 1; $i <= $this->totalPage; $i ++) {
          $this->viewNumbers [] = $i;
         }
       } else {
         $this->getViewNums ( 
                1, 
                $this->totalPage, 
                $this->currentPage, 
                $this->viewCount
         );
       }
    }

    /**
     * 获取当前显示的页面
     * 进入此方法前置条件：总页码大于要显示的页码数了
     *
     * @param int $min
     *       最小页码
     * @param int $max
     *       最大页面
     * @param int $current
     *       当前页面
     * @param int $viewCount
     *       显示页码数
     */
    private function getViewNums($min, $max, $current, $viewCount) {
       if ( empty ( $min ) || 
            empty ( $max ) || 
            empty ( $current ) || 
            empty ( $viewCount )
          ) {
         return;
       }
       if ($max < $min) {
         return;
       }

       if ($current == $min) { // 当前为第一页
         $this->viewNumbers = array (
              $min,
              $min + 1,
              $min + 2,
              $min + 3,
              $min + 4
         );
         return;
       }
       if ($current == $max) { // 当前为最大页
         $this->viewNumbers = array (
              $max - 4,
              $max - 3,
              $max - 2,
              $max - 1,
              $max
         );
         return;
       }
       if (($current - $min) >= 2 && ($max - $current) >= 2) { //当前页码正好处于中间
         $this->viewNumbers = array (
              $current - 2,
              $current - 1,
              $current,
              $current + 1,
              $current + 2
         );
         return;
       }
       if (($current - $min) < 2) { // 距离最小不到2
         $this->viewNumbers = array (
              $current - 1,
              $current,
              $current + 1,
              $current + 2,
              $current + 3
         );
         return;
       }
       if (($max - $current) < 2) { // 距离最大不到2
         $this->viewNumbers = array (
              $current - 3,
              $current - 2,
              $current - 1,
              $current,
              $current + 1
         );
         return;
       }
    }

    /**
     * 设置可返回的url
     */
    private function _get_back_url() {
      $jumpUrl = '';
      if(IS_AJAX || IS_POST) {
          if(isset($_SERVER['HTTP_REFERER'])) {
              $jumpUrl = urlencode($_SERVER['HTTP_REFERER']);
          }
      }else {
          if(isset($_SERVER['REQUEST_URI'])) {
              $jumpUrl = urlencode($_SERVER['REQUEST_URI']);
          }
      }
      $this->url = $jumpUrl;
    }
}
```

**注:** 使用方法大家自己悟吧!

Author [@Snaker95][1]

[1]: http://www.sharedsea.com