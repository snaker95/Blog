---
title: PHP 写日志方法
categories: PHP
tags: PHP
keywords: [PHP,log,日志,log_message]
description: 程序运行过程中总会出现错误, 在排除错误的过程中, 查看日志是很重要的, 所以咱们PHP程序猿也要学会写log日志来帮助排错

---

[TOC]

# PHP 写日志方法
程序运行过程中总会出现错误, 在排除错误的过程中, 查看日志是很重要的, 所以咱们PHP程序猿也要学会写log日志来帮助排错, 大多数PHP框架中会有相关方法, 不过也可以自己写方法来实现嘛~
<!--more-->
直接上代码:

```php
class Log_message {
  /**
   * @param log_time_format 时间格式化
   * @param log_file_size   日志文件限制
   * @param log_path        写入日志目录
  **/
  protected $config  =   array(
    'log_time_format'   =>  ' c ',
    'log_file_size'     =>  2097152,
    'log_path'          =>  '',
  );

  /** 实例化并传入参数
   * @param log              日志内容
   * @param destinatin       日志级别
  **/
  public function __construct($log, $destination='', $config=array()){
    $this->config   =   array_merge($this->config,$config);
    $this->_write_($log, $destination);
  }

  /**
   * 日志写入接口
   * @access public
   * @param string $log 日志信息
   * @param string $destination  写入目标
   * @return void
   */
  protected function _write_($log,$destination='') {
    $now = date($this->config['log_time_format']);
    if(empty($destination)){
      $destination = $this->config['log_path'].date('y_m_d').'.log';
    }
    // 自动创建日志目录
    $log_dir = dirname($destination);
      if (!is_dir($log_dir)) {
        mkdir($log_dir, 0755, true);
      }        
      //检测日志文件大小，超过配置大小则备份日志文件重新生成
      if(
        is_file($destination)
        && floor($this->config['log_file_size']) <= filesize($destination)
      ){
        $rename = dirname($destination).'/'.time().'-'.basename($destination);
        rename($destination, $rename);
      }
      $log_format = "[{$now}] ";
      $log_format .= $_SERVER['REMOTE_ADDR'].' ';
      $log_format .= $_SERVER['REQUEST_URI']."\r\n{$log}\r\n";
      error_log($log_format, 3,$destination);
  }
}
```

**讲解:** 1. 使用方法: 

```php
  new Log_message($log);    
  // OR
  new Log_message($log, $desc); 
  // OR
  # 配置参数
  $config = array(
        'log_time_format'   =>  ' Y-m-d H:i:s ',
        'log_file_size'     =>  2097152,
        'log_path'          =>  '',
    );
  new Log_message($log, $desc, $config);
```

Author [@Snaker95][1]

[1]: http://www.sharedsea.com