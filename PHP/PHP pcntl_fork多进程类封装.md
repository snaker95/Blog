---
title: PHP pcntl_fork多进程类封装
categories: PHP
tags: PHP
keywords: [PHP, 多进程]
description: 在执行php脚本的时候, 多进程很漂亮的解决效率问题

---

<!--more-->

[TOC]
# PHP pcntl_fork多进程类封装
废话就多说了, 直接上代码:

## 代码部分
```php
<?php
declare(ticks = 1);
class PcntlForkClass {
    static $master = true;
    static $worker = true;
    static $argc = 0;
    static $argv = array();
    static $pcntlForkClass = null;
    static $c = 4; // 进程数
    static $pids = array();
    static $forkname = '';
    static $callback;
    /**
     * 
     */
    public function __construct($argc=0, $argv=array()) {
        self::$argc = (int)$argc;
        self::$argv = is_array($argv)?$argv:array();
    }

    /**
     *
     */
    public function __invoke($forkname, $file=__FILE__, $callback='callback') {
        self::$forkname = $forkname;
        self::$callback = $callback;

        $this->init($file);//初始化
        $this->setproctitle(self::$forkname.' : master'); // 设置title
        $this->makesig(array(
                SIGHUP,
                SIGTERM,
                SIGQUIT
            ), 'PcntlForkClass::master'); // 打标记
        $this->fork(self::$c); // 创建进程集合

        $today = date('Y-m-d');
        $sign = md5_file($file);
        for(; self::$master;){
            if(date('Y-m-d') != $today || md5_file($file) != $sign){
                // Logger::info('detected file or date changed will exit!');
                posix_kill(0, SIGUSR1);
                exit(0);
            }
            
            $str = shell_exec("ps aux | grep '".self::$forkname."' | grep -v grep | awk '{print $2}'");
            $str = trim($str);
            if(strlen($str) > 0){
                $tmp = explode("\n", $str);
                foreach($pids as $pid){
                    if(!in_array($pid, $tmp)){
                        // Logger::info("worker pid=%d maybe died", $pid);
                        unset($pids[$pid]);
                        $pid = $this->makeproc();
                        $pids[$pid] = $pid;
                    }
                }
            }
            sleep(1);
        }
    }

    /*
     * 关闭静态调用
     */
    // static function run($argc=0, $argv=array()) {
    //  !(self::$pcntlForkClass instanceof PcntlForkClass) && self::$pcntlForkClass = new self($argc, $argv);
    //  return self::$pcntlForkClass;
    // }

    /**
     * 初始化
     * 进程数
     */
    private function init($file) {
        if(!function_exists('shell_exec')){
            fwrite(STDOUT, "shell_exec functions are available.\n");
            exit(0);
        }
        // 进程数
        $argc = self::$argc;
        $argv = self::$argv;
        for($i = 1; $i < $argc; $i++){
            $arg = strtolower($argv[$i]);
            if($i + 1 <= $argc && ('-p' == $arg || '--process' == $arg))
                self::$c = intval($argv[++$i]);
            else
                $this->help($file);
        }
        self::$c>4 && self::$c=4;
    }

    /**
     * 创建进程并执行任务
     * 
     */
    private function fork($c){
        for($i = 0; $i < $c; $i++){
            $pid = $this->makeproc(); // 开启进程
            self::$pids[$pid] = $pid;
        }
    }

    /**
     * 创建进程任务
     * 
     */
    private function makeproc(){
        $pid = pcntl_fork();
        if(-1 == $pid){ // 创建进程失败
            // Logger::Fatal("fork child process FAIL");
            exit(0);
        }elseif(!$pid){ // 子进程
            $this->setproctitle(self::$forkname.' : worker');
            $this->makesig(array(
                SIGUSR1
            ), 'PcntlForkClass::worker');
            $callback = self::$callback; // 处理数据主体
            $callback(self::$worker);
            exit(0);
        }
        // 主进程id
        return $pid;
    }


    private function makesig(array $sig, $handler){
        foreach($sig as $s){
            pcntl_signal($s, $handler);
        }
    }

    static function worker($signo){
        if(SIGUSR1 == $signo){
            // Logger::info("worker recv signal %d", $signo);
            self::$worker = false;
            $this->setproctitle(self::$forkname.' : worker closing');
        }
    }

    static function master($signo){
        switch($signo){
            case SIGHUP:
            case SIGKILL:
            case SIGTERM:
            case SIGQUIT:
                // Logger::info("master recv signal %d", $signo);
                $this->setproctitle(self::$forkname.' : master closing');
                self::$master = false;
                posix_kill(0, SIGUSR1); // 发送信号
                sleep(5);
        }
    }

    private function setproctitle($title) {
        if(function_exists('cli_set_process_title')){ // 可能存在不允许修改进程名称
            @cli_set_process_title($title);
        } else if(function_exists('setproctitle')){
            @setproctitle($title);
        }
    }


    private function help($file) {
        print("Usage: php -f $file [OPTIONS]\n");
        print("Options are:\n");
        print("-h, --help\tshow this help message\n");
        print("-p, --process\tstart process to send ".self::$forkname." default is 1 process\n");
        exit(0);
    }
}
set_time_limit(86400);
ini_set('max_execution_time', 86400);


// $fork = new PcntlForkClass($argc, $argv);
// function a($worker) {
//  print 'qq';
// }
// $fork('forkname', 'filepath', 'callback');
```
* 备注: 希望以后使用时提高效率