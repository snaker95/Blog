# Mac OS找不到/usr/include文件夹的解决办法

[TOC]

## 问题引出：

安装php插件：

phpize提示：
```shell
grep: /usr/include/php/main/php.h: No such file or directory
grep: /usr/include/php/Zend/zend_modules.h: No such file or directory
grep: /usr/include/php/Zend/zend_extensions.h: No such file or directory
make后报错：

fatal error: 'php.h' file not found
```
究其原因：

``/usr/include`文件夹不存在，而且由于MAC OS SIP(System Integrity Protection)机制，没有权限在/usr/下创建文件/软链接。此问题也会导致其他开发的错误。

## Mojave 10.14最新解决方案：

终端执行：

```shell
xcode-select --install
#完成后执行
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /
```

结果：此时/usr/include应该就出现了。

10.15中苹果为了同时运行不同版本的SDK和Xcode，完全移除了/usr/include

## Catalina 10.15解决方案(需要关闭SIP)：

```shell
#command+R 进入恢复模式后关闭SIP。修改完成后建议启用。
csrutil disable

#已安装可以无视
xcode-select --install

#根目录挂载为可读写
sudo mount -uw /

#建立软链
sudo ln -s "$(xcrun --show-sdk-path)/usr/include" /usr/include
```