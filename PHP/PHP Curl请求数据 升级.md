---
title: PHP Curl请求数据<升级>
categories: PHP
tags: PHP
keywords: [PHP,CURL]
description: PHP已是公认世界上最好的语言, PHP中的CURL扩展帮助猿类们实现后台请求其他接口, 或者请求网络中其他的数据, 此时, 封装CURL成为一个函数方法, 简化在使用中的复杂程度

---

[TOC]
# PHP Curl请求数据<升级>
废话不说,直接上代码, 不过还要啰嗦一下, 该方法可实现cookie传递即完全仿浏览器
```php
// lib/requests.library.php
// +----------------------------------------------------------------------
// | PHPSpider [ A PHP Framework For Crawler ]
// +----------------------------------------------------------------------
// | Copyright (c) 2006-2014 https://doc.phpspider.org All rights reserved.
// +----------------------------------------------------------------------
// | Licensed ( http://www.apache.org/licenses/LICENSE-2.0 )
// +----------------------------------------------------------------------
// | Author: Seatle Yang <seatle@foxmail.com>
// +----------------------------------------------------------------------
//----------------------------------
// PHPSpider请求类文件
//----------------------------------
// namespace lib;
if (!function_exists('curl_file_create'))
{
    function curl_file_create($filename, $mimetype = '', $postname = '')
    {
        return "@$filename;filename="
            . ($postname ?: basename($filename))
            . ($mimetype ? ";type=$mimetype" : '');
    }
}
class requests
{
    const VERSION = '2.0.0';
    protected static $ch = null;
    /**** Public variables ****/
    /* user definable vars */
    public static $timeout = 5;
    public static $encoding = null;
    public static $input_encoding = null;
    public static $output_encoding = null;
    public static $cookies = array();                           // array of cookies to pass
    public static $domain = '';
    // $cookies['username'] = "seatle";
    public static $rawheaders = array();                        // array of raw headers to send
    public static $domain_cookies = array();                    // array of cookies for domain to pass
    public static $hosts = array();                             // random host binding for make request faster
    public static $headers = array();                           // headers returned from server sent here
    public static $useragents = array(
                "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36",
                "Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_3 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13G34 Safari/601.1",
                "Mozilla/5.0 (Linux; U; Android 6.0.1;zh_cn; Le X820 Build/FEXCNFN5801507014S) AppleWebKit/537.36 (KHTML, like Gecko)Version/4.0 Chrome/49.0.0.0 Mobile Safari/537.36 EUI Browser/5.8.015S"
                );        // random agent we masquerade as
    public static $client_ips = array();                        // random ip we masquerade as
    public static $proxies = array();                           // random proxy ip
    public static $raw = "";                                    // head + body content returned from server sent here
    public static $head = "";                                   // head content
    public static $content = "";                                // The body before encoding
    public static $text = "";                                   // The body after encoding
    public static $info = array();                              // curl info
    public static $history = 302;                               // http request status before redirect. ex:30x
    public static $status_code = 0;                             // http request status
    public static $error = "";                                  // error messages sent here
    /**
     * set timeout
     * $timeout 为数组时会分别设置connect和read
     *
     * @param init or array $timeout
     * @return
     */
    public static function set_timeout($timeout)
    {
        self::$timeout = $timeout;
    }
    /**
     * 设置代理
     * 如果代理有多个，请求时会随机使用
     *
     * @param mixed $proxies
     * array (
     *    'socks5://user1:pass2@host:port',
     *    'socks5://user2:pass2@host:port'
     *)
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2016-09-18 10:17
     */
    public static function set_proxy($proxy)
    {
        self::$proxies = is_array($proxy) ? $proxy : array($proxy);
    }
    /**
     * 自定义请求头部
     * 请求头内容可以用 requests::$rawheaders 来获取
     * 比如获取Content-Type：requests::$rawheaders['Content-Type']
     *
     * @param string $headers
     * @return void
     */
    public static function set_header($key, $value)
    {
        self::$rawheaders[$key] = $value;
    }
    /**
     * 设置全局COOKIE
     *
     * @param string $cookie
     * @return void
     */
    public static function set_cookie($key, $value, $domain = '')
    {
        if (empty($key))
        {
            return false;
        }
        if (!empty($domain))
        {
            self::$domain_cookies[$domain][$key] = $value;
        }
        else
        {
            self::$cookies[$key] = $value;
        }
        return true;
    }
    /**
     * 批量设置全局cookie
     *
     * @param mixed $cookies
     * @param string $domain
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function set_cookies($cookies, $domain = '')
    {
        $cookies_arr = explode(";", $cookies);
        if (empty($cookies_arr))
        {
            return false;
        }
        foreach ($cookies_arr as $cookie)
        {
            $cookie_arr = explode("=", $cookie, 2);
            $key = $cookie_arr[0];
            $value = empty($cookie_arr[1]) ? '' : $cookie_arr[1];
            if (!empty($domain))
            {
                self::$domain_cookies[$domain][$key] = $value;
            }
            else
            {
                self::$cookies[$key] = $value;
            }
        }
        return true;
    }
    /**
     * 获取单一Cookie
     *
     * @param mixed $name    cookie名称
     * @param string $domain 不传则取全局cookie，就是手动set_cookie的cookie
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function get_cookie($name, $domain = '')
    {
        if (!empty($domain) && !isset(self::$domain_cookies[$domain]))
        {
            return '';
        }
        $cookies = empty($domain) ? self::$cookies : self::$domain_cookies[$domain];
        return isset($cookies[$name]) ? $cookies[$name] : '';
    }

    /**
     * 获取Cookie数组
     *
     * @param string $domain 不传则取全局cookie，就是手动set_cookie的cookie
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function get_cookies($domain = '')
    {
        if (!empty($domain) && !isset(self::$domain_cookies[$domain]))
        {
            return array();
        }
        return empty($domain) ? self::$cookies : self::$domain_cookies[$domain];
    }
    /**
     * 删除Cookie
     *
     * @param string $domain  不传则删除全局Cookie
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function del_cookie($key, $domain = '')
    {
        if (empty($key))
        {
            return false;
        }
        if (!empty($domain) && !isset(self::$domain_cookies[$domain]))
        {
            return false;
        }
        if (!empty($domain))
        {
            if (isset(self::$domain_cookies[$domain][$key]))
            {
                unset(self::$domain_cookies[$domain][$key]);
            }
        }
        else
        {
            if (isset(self::$cookies[$key]))
            {
                unset(self::$cookies[$key]);
            }
        }
        return true;
    }
    /**
     * 删除Cookie
     *
     * @param string $domain  不传则删除全局Cookie
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function del_cookies($domain = '')
    {
        if (!empty($domain) && !isset(self::$domain_cookies[$domain]))
        {
            return false;
        }
        if ( empty($domain) )
        {
            self::$cookies = array();
        }
        else
        {
            if (isset(self::$domain_cookies[$domain]))
            {
                unset(self::$domain_cookies[$domain]);
            }
        }
        return true;
    }
    /**
     * 设置随机的user_agent
     *
     * @param string $useragent
     * @return void
     */
    public static function set_useragent($useragent)
    {
        self::$useragents = is_array($useragent) ? $useragent : array($useragent);
    }
    /**
     * set referer
     *
     */
    public static function set_referer($referer)
    {
        self::$rawheaders['Referer'] = $referer;
    }
    /**
     * 设置伪造IP
     * 传入数组则为随机IP
     * @param string $ip
     * @return void
     */
    public static function set_client_ip($ip)
    {
        self::$client_ips = is_array($ip) ? $ip : array($ip);
    }
    /**
     * 设置Hosts
     * 负载均衡到不同的服务器，如果对方使用CDN，采用这个是最好的了
     *
     * @param string $hosts
     * @return void
     */
    public static function set_hosts($host, $ips = array())
    {
        $ips = is_array($ips) ? $ips : array($ips);
        self::$hosts[$host] = $ips;
    }
    /**
     * 分割返回的header和body
     * header用来判断编码和获取Cookie
     * body用来判断编码，得到编码前和编码后的内容
     *
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function split_header_body($domain='')
    {
        $head = $body = '';
        $head = substr(self::$raw, 0, self::$info['header_size']);
        $body = substr(self::$raw, self::$info['header_size']);
        // http header
        self::$head = $head;
        // The body before encoding
        self::$content = $body;

        // 如果用户没有明确指定输入的页面编码格式(utf-8, gb2312)，通过程序去判断
        if(self::$input_encoding == null)
        {
            // 从头部获取
            preg_match("/charset=([^\s]*)/i", $head, $out);
            $encoding = empty($out[1]) ? '' : str_replace(array('"', '\''), '', strtolower(trim($out[1])));
            //$encoding = null;
            if (empty($encoding))
            {
                // 在某些情况下,无法再 response header 中获取 html 的编码格式
                // 则需要根据 html 的文本格式获取
                $encoding = self::get_encoding($body);
                $encoding = strtolower($encoding);
                if($encoding == false || $encoding == "ascii")
                {
                    $encoding = 'gbk';
                }
            }
            // 没有转码前
            self::$encoding = $encoding;
            self::$input_encoding = $encoding;
        }
        // 设置了输出编码的转码，注意: xpath只支持utf-8，iso-8859-1 不要转，他本身就是utf-8
        if (self::$output_encoding && self::$input_encoding != self::$output_encoding && self::$input_encoding != 'iso-8859-1')
        {
            // 先将非utf8编码,转化为utf8编码
            $body = @mb_convert_encoding($body, self::$output_encoding, self::$input_encoding);
            // 将页面中的指定的编码方式修改为utf8
            $body = preg_replace("/<meta([^>]*)charset=([^>]*)>/is", '<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>', $body);

            // 直接干掉头部，国外很多信息是在头部的
            //$body = self::_remove_head($body);
            // 转码后
            self::$encoding = self::$output_encoding;
        }
        // The body after encoding
        self::$text = $body;
        return array($head, $body);
    }
    /**
     * 获得域名相对应的Cookie
     *
     * @param mixed $header
     * @param mixed $domain
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function get_response_cookies($header, $domain)
    {
        // 解析Cookie并存入 self::$cookies 方便调用
        preg_match_all("/.*?Set\-Cookie: ([^\r\n]*)/i", $header, $matches);
        $cookies = empty($matches[1]) ? array() : $matches[1];
        // 解析到Cookie
        if (!empty($cookies))
        {
            $cookies = implode(";", $cookies);
            $cookies = explode(";", $cookies);
            foreach ($cookies as $cookie)
            {
                $cookie_arr = explode("=", $cookie, 2);
                // 过滤 httponly、secure
                if (count($cookie_arr) < 2)
                {
                    continue;
                }
                $cookie_name = !empty($cookie_arr[0]) ? trim($cookie_arr[0]) : '';
                if (empty($cookie_name))
                {
                    continue;
                }
                // 过滤掉domain路径
                if (in_array(strtolower($cookie_name), array('path', 'domain', 'expires', 'max-age')))
                {
                    continue;
                }
                self::$domain_cookies[$domain][trim($cookie_arr[0])] = trim($cookie_arr[1]);
            }
        }
    }
    /**
     * 获得response header
     * 此方法占时没有用到
     *
     * @param mixed $header
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function get_response_headers($header)
    {
        $headers = array();
        $header_lines = explode("\n", $header);
        if (!empty($header_lines))
        {
            foreach ($header_lines as $line)
            {
                $header_arr = explode(":", $line, 2);
                $key = empty($header_arr[0]) ? '' : trim($header_arr[0]);
                $val = empty($header_arr[1]) ? '' : trim($header_arr[1]);
                if (empty($key) || empty($val))
                {
                    continue;
                }
                $headers[$key] = $val;
            }
        }
        self::$headers = $headers;
        return self::$headers;
    }
    /**
     * 获取编码
     * @param $string
     * @return string
     */
    public static function get_encoding($string)
    {
        $encoding = mb_detect_encoding($string, array('UTF-8', 'GBK', 'GB2312', 'LATIN1', 'ASCII', 'BIG5'));
        return strtolower($encoding);
    }
    /**
     * 移除页面head区域代码
     * @param $html
     * @return mixed
     */
    private static function _remove_head($html)
    {
        return preg_replace('/<head.+?>.+<\/head>/is', '<head></head>', $html);
    }

    /**
     * 简单的判断一下参数是否为一个URL链接
     * @param  string  $str
     * @return boolean
     */
    private static function _is_url($url)
    {
        //$pattern = '/^http(s)?:\\/\\/.+/';
        $pattern = "/\b(([\w-]+:\/\/?|www[.])[^\s()<>]+(?:\([\w\d]+\)|([^[:punct:]\s]|\/)))/";
        if (preg_match($pattern, $url))
        {
            return true;
        }
        return false;
    }
    /**
     * 初始化 CURL
     *
     */
    public static function init()
    {
        if (!is_resource ( self::$ch ))
        {
            self::$ch = curl_init ();
            curl_setopt( self::$ch, CURLOPT_RETURNTRANSFER, true );
            curl_setopt( self::$ch, CURLOPT_HEADER, false );
            curl_setopt( self::$ch, CURLOPT_USERAGENT, "phpspider-requests/".self::VERSION );
            // 如果设置了两个时间，就分开设置
            if (is_array(self::$timeout))
            {
                curl_setopt( self::$ch, CURLOPT_CONNECTTIMEOUT, self::$timeout[0] );
                curl_setopt( self::$ch, CURLOPT_TIMEOUT, self::$timeout[1]);
            }
            else
            {
                curl_setopt( self::$ch, CURLOPT_CONNECTTIMEOUT, self::$timeout );
                curl_setopt( self::$ch, CURLOPT_TIMEOUT, self::$timeout);
            }
            // 在多线程处理场景下使用超时选项时，会忽略signals对应的处理函数，但是无耐的是还有小概率的crash情况发生
            curl_setopt( self::$ch, CURLOPT_NOSIGNAL, true);
        }
        return self::$ch;
    }
    /**
     * get 请求
     */
    public static function get($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'get', $fields, NULL, $allow_redirects, $cert);
    }
    /**
     * post 请求
     * $fields 有三种类型:1、数组；2、http query；3、json
     * 1、array('name'=>'yangzetao')
     * 2、http_build_query(array('name'=>'yangzetao'))
     * 3、json_encode(array('name'=>'yangzetao'))
     * 前两种是普通的post，可以用$_POST方式获取
     * 第三种是post stream( json rpc，其实就是webservice )
     * 虽然是post方式，但是只能用流方式 http://input 后者 $HTTP_RAW_POST_DATA 获取
     *
     * @param mixed $url
     * @param array $fields
     * @param mixed $proxies
     * @static
     * @access public
     * @return void
     */
    public static function post($url, $fields = array(), $files = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'POST', $fields, $files, $allow_redirects, $cert);
    }
    public static function put($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'PUT', $fields, $allow_redirects, $cert);
    }
    public static function delete($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'DELETE', $fields, $allow_redirects, $cert);
    }
    // 响应HTTP头域里的元信息
    // 此方法被用来获取请求实体的元信息而不需要传输实体主体（entity-body）
    // 此方法经常被用来测试超文本链接的有效性，可访问性，和最近的改变。.
    public static function head($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        self::request($url, 'HEAD', $fields, $allow_redirects, $cert);
    }
    public static function options($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'OPTIONS', $fields, $allow_redirects, $cert);
    }
    public static function patch($url, $fields = array(), $allow_redirects = true, $cert = NULL)
    {
        self::init ();
        return self::request($url, 'PATCH', $fields, $allow_redirects, $cert);
    }
    /**
     * request
     *
     * @param mixed $url        请求URL
     * @param string $method    请求方法
     * @param array $fields     表单字段
     * @param array $files      上传文件
     * @param mixed $cert       CA证书
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function request($url, $method = 'GET', $fields = array(), $files = array(), $allow_redirects = true, $cert = NULL)
    {
        $method = strtoupper($method);
        if(!self::_is_url($url))
        {
            self::$error = "You have requested URL ({$url}) is not a valid HTTP address";
            return false;
        }
        // 如果是 get 方式，直接拼凑一个 url 出来
        if ($method == 'GET' && !empty($fields))
        {
            $url = $url . (strpos($url,"?")===false ? "?" : "&") . http_build_query($fields);
        }
        $parse_url = parse_url($url);
        if (empty($parse_url) || empty($parse_url['host']) || !in_array($parse_url['scheme'], array('http', 'https')))
        {
            self::$error = "No connection adapters were found for '{$url}'";
            return false;
        }
        $scheme = $parse_url['scheme'];
        self::$domain = $domain = $parse_url['host'];

        // 随机绑定 hosts，做负载均衡
        if (self::$hosts)
        {
            if (isset(self::$hosts[$domain]))
            {
                $hosts = self::$hosts[$domain];
                $key = rand(0, count($hosts)-1);
                $ip = $hosts[$key];
                $url = str_replace($domain, $ip, $url);
                self::$rawheaders['Host'] = $domain;
            }
        }
        curl_setopt( self::$ch, CURLOPT_URL, $url );
        if ($method != 'GET')
        {
            // 如果是 post 方式
            if ($method == 'POST')
            {
                curl_setopt( self::$ch, CURLOPT_POST, true );
                $file_fields = array();
                if (!empty($files))
                {
                    foreach ($files as $postname => $file)
                    {
                        $filepath = realpath($file);
                        // 如果文件不存在
                        if (!file_exists($filepath))
                        {
                            continue;
                        }
                        $filename = basename($filepath);
                        $type = self::get_mimetype($filepath);
                        $file_fields[$postname] = curl_file_create($filepath, $type, $filename);
                        // curl -F "name=seatle&file=@/absolute/path/to/image.png" htt://localhost/uploadfile.php
                        //$cfile = '@'.realpath($filename).";type=".$type.";filename=".$filename;
                    }
                }
            }
            else
            {
                self::$rawheaders['X-HTTP-Method-Override'] = $method;
                curl_setopt( self::$ch, CURLOPT_CUSTOMREQUEST, $method );
            }
            if (!empty($fields))
            {
                // 不是上传文件的，用http_build_query, 能实现更好的兼容性，更小的请求数据包
                if ( empty($file_fields) )
                {
                    // post方式
                    if ( is_array($fields) )
                    {
                        $fields = http_build_query($fields);
                    }
                }
                else
                {
                    // 有post数据
                    if ( is_array($fields) && !empty($fields) )
                    {
                        // 某些server可能会有问题
                        $fields = array_merge($fields, $file_fields);
                    }
                    else
                    {
                        $fields = $file_fields;
                    }
                }
                // 不能直接传数组，不知道是什么Bug，会非常慢
                curl_setopt( self::$ch, CURLOPT_POSTFIELDS, $fields );
            }
        }
        $cookies = self::get_cookies();
        $domain_cookies = self::get_cookies($domain);
        $cookies =  array_merge($cookies, $domain_cookies);
        // 是否设置了cookie
        if (!empty($cookies))
        {
            foreach ($cookies as $key=>$value)
            {
                $cookie_arr[] = $key."=".$value;
            }
            $cookies = implode("; ", $cookie_arr);
            curl_setopt( self::$ch, CURLOPT_COOKIE, $cookies );
        }
        if (!empty(self::$useragents))
        {
            $key = rand(0, count(self::$useragents) - 1);
            self::$rawheaders['User-Agent'] = self::$useragents[$key];
        }
        if (!empty(self::$client_ips))
        {
            $key = rand(0, count(self::$client_ips) - 1);
            self::$rawheaders["CLIENT-IP"] = self::$client_ips[$key];
            self::$rawheaders["X-FORWARDED-FOR"] = self::$client_ips[$key];
        }
        if (self::$rawheaders)
        {
            $headers = array();
            foreach (self::$rawheaders as $k=>$v)
            {
                $headers[] = $k.": ".$v;
            }
            curl_setopt( self::$ch, CURLOPT_HTTPHEADER, $headers );
        }
        curl_setopt( self::$ch, CURLOPT_ENCODING, 'gzip' );
        // 关闭验证
        if ($scheme == 'https')
        {
            curl_setopt(self::$ch, CURLOPT_SSL_VERIFYPEER, false);
            curl_setopt(self::$ch, CURLOPT_SSL_VERIFYHOST, false);
        }
        if (self::$proxies)
        {
            $key = rand(0, count(self::$proxies) - 1);
            $proxy = self::$proxies[$key];
            curl_setopt( self::$ch, CURLOPT_PROXY, $proxy );
        }
        // header + body，header 里面有 cookie
        curl_setopt( self::$ch, CURLOPT_HEADER, true );
        // 请求跳转后的内容
        if ($allow_redirects)
        {
            curl_setopt( self::$ch, CURLOPT_FOLLOWLOCATION, true);
        }
        self::$raw = curl_exec ( self::$ch );
        // 真实url
        //$location = curl_getinfo( self::$ch, CURLINFO_EFFECTIVE_URL);
        self::$info = curl_getinfo( self::$ch );
        //print_r(self::$info);
        self::$status_code = self::$info['http_code'];
        if (self::$raw === false)
        {
            self::$error = 'Curl error: ' . curl_error( self::$ch );
            //trigger_error(self::$error, E_USER_WARNING);
        }
        // 关闭句柄
        curl_close( self::$ch );
        // 请求成功之后才把URL存起来
        list($header, $text) = self::split_header_body($domain);
        self::$history = self::get_history($header);
        self::$headers = self::get_response_headers($header);
        self::get_response_cookies($header, $domain);
        //$data = substr($data, 10);
        //$data = gzinflate($data);
        return $text;
    }
    public static function get_history($header)
    {
        $status_code = 0;
        $lines = explode("\n", $header);
        foreach ($lines as $line)
        {
            $line = trim($line);
            if (preg_match("#^HTTP/.*? (\d+) Found#", $line, $out))
            {
                $status_code = empty($out[1]) ? 0 : intval($out[1]);
            }
        }
        return $status_code;
    }
    // 获取 mimetype
    public static function get_mimetype($filepath)
    {
        $fp  = finfo_open(FILEINFO_MIME);
        $mime = finfo_file($fp, $filepath);
        finfo_close($fp);
        $arr = explode(";", $mime);
        $type = empty($arr[0]) ? '' : $arr[0];
        return $type;
    }
    /**
     * 拼凑文件和表单
     * 占时没有用到
     *
     * @param mixed $post_fields
     * @param mixed $file_fields
     * @return void
     * @author seatle <seatle@foxmail.com>
     * @created time :2017-08-03 18:06
     */
    public static function get_postfile_form($post_fields, $file_fields)
    {
        // 构造post数据
        $data = '';
        $delimiter = '-------------' . uniqid();
        // 表单数据
        foreach ($post_fields as $name => $content)
        {
            $data .= "--" . $delimiter . "\r\n";
            $data .= 'Content-Disposition: form-data; name = "' . $name . '"';
            $data .= "\r\n\r\n";
            $data .= $content;
            $data .= "\r\n";
        }
        foreach ($file_fields as $input_name => $file)
        {
            $data .= "--" . $delimiter . "\r\n";
            $data .= 'Content-Disposition: form-data; name = "' . $input_name . '";' .
                ' filename="' . $file['filename'] . '"' . "\r\n";
            $data .= "Content-Type: {$file['type']}\r\n";
            $data .= "\r\n";
            $data .= $file['content'];
            $data .= "\r\n";
        }
        // 结束符
        $data .= "--" . $delimiter . "--\r\n";
        //return array(
            //CURLOPT_HTTPHEADER => array(
                //'Content-Type:multipart/form-data;boundary=' . $delimiter,
                //'Content-Length:' . strlen($data)
            //),
            //CURLOPT_POST => true,
            //CURLOPT_POSTFIELDS => $data,
        //);
        return array($delimiter, $data);
    }
}
```
* 备注: 来至PHPspider
调用方法参考:
```php
include __DIR__.'/lib/requests.library.php';
function request_url($url, $link = array())
{
    $time_start = microtime(true);
    $agent_pc = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36";
    $agent_IOS = "Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_3 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13G34 Safari/601.1";
    $agent_andoid = "Mozilla/5.0 (Linux; U; Android 6.0.1;zh_cn; Le X820 Build/FEXCNFN5801507014S) AppleWebKit/537.36 (KHTML, like Gecko)Version/4.0 Chrome/49.0.0.0 Mobile Safari/537.36 EUI Browser/5.8.015S";
    $ip = null;
    //$url = "http://www.qiushibaike.com/article/117568316";
    // 设置了编码就不要让requests去判断了
    requests::$input_encoding = 'utf-8';
    // 得到的编码如果不是utf-8的要转成utf-8, 因为xpath只支持utf-8
    requests::$output_encoding = 'utf-8';
    requests::set_timeout(20);
    requests::set_useragent($agent_pc);
    if (isset($ip))
    {
        requests::set_client_ip(isset($ip));
    }
    // 是否设置了代理
    if (isset($link['proxy']))
    {
        requests::set_proxy($link['proxy']);
    }
    // 如何设置了 HTTP Headers
    if (!empty($link['headers']))
    {
        foreach ($link['headers'] as $k=>$v)
        {
            requests::set_header($k, $v);
        }
    }
    $method = empty($link['method']) ? 'get' : strtolower($link['method']);
    $params = empty($link['params']) ? array() : $link['params'];

    $html = requests::$method($url, $params);
    // var_dump(requests::$domain_cookies);
    // 此url附加的数据不为空, 比如内容页需要列表页一些数据, 拼接到后面去
    if ($html && !empty($link['context_data']))
    {
        $html .= $link['context_data'];
    }
    $http_code = requests::$status_code;
    // if ($this->on_status_code)
    // {
    //     $return = call_user_func($this->on_status_code, $http_code, $url, $html, $this);
    //     if (isset($return))
    //     {
    //         $html = $return;
    //     }
    //     if (!$html)
    //     {
    //         return false;
    //     }
    // }
    if ($http_code != 200)
    {
        // 如果是301、302跳转, 抓取跳转后的网页内容
        if ($http_code == 301 || $http_code == 302)
        {
            $info = requests::$info;
            //if (isset($info['redirect_url']))
            if (!empty($info['redirect_url']))
            {
                $url = $info['redirect_url'];
                requests::$input_encoding = null;
                $method = empty($link['method']) ? 'get' : strtolower($link['method']);
                $params = empty($link['params']) ? array() : $link['params'];
                $html = requests::$method($url, $params);
                // 有跳转的就直接获取就好，不要调用自己，容易进入死循环
                //$html = $this->request_url($url, $link);
                if ($html && !empty($link['context_data']))
                {
                    $html .= $link['context_data'];
                }
            }
            else
            {
                return false;
            }
        }
        else
        {
            if ($http_code == 407)
            {
                // 扔到队列头部去, 继续采集
                $this->queue_rpush($link);
                log::error("Failed to download page {$url}");
                self::$collect_fail++;
            }
            elseif (in_array($http_code, array('0','502','503','429')))
            {
                // 采集次数加一
                $link['try_num']++;
                // 抓取次数 小于 允许抓取失败次数
                if ( $link['try_num'] <= $link['max_try'] )
                {
                    // 扔到队列头部去, 继续采集
                    $this->queue_rpush($link);
                }
                log::error("Failed to download page {$url}, retry({$link['try_num']})");
            }
            else
            {
                log::error("Failed to download page {$url}");
                self::$collect_fail++;
            }
            log::error("HTTP CODE: {$http_code}");
            return false;
        }
    }
    // 爬取页面耗时时间
    $time_run = round(microtime(true) - $time_start, 3);
    return $html;
}
$url = 'http://www.baidu.com';
request_url($url);
request_url($url); // 测试多次请求是否携带cookies
$url = 'http://wx.gcjxgj.cn';
request_url($url);
request_url($url);
```