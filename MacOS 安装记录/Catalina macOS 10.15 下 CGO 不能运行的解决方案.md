# Catalina macOS 10.15 下 CGO 不能运行的解决方案

[TOC]

**如果提示：**

```shell
# crypto/x509
share/go/src/crypto/x509/root_cgo_darwin.go:16:10: fatal error: 'CoreFoundation/CoreFoundation.h' file not found
#include <CoreFoundation/CoreFoundation.h>
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

**或**：

```shell
fatal error: ‘stdio.h’ file not found
```

首先应该安装最新稳定版的 Xcode，安装完毕后打开运行，会提示安装 Command Line Tools，或运行：

```shell
xcode-select --install
```

此时运气好问题就会消失了，如果仍然提示 CoreFoundation .h 找不到，应按照以下方案依次运行：

1. ### 重启，按住 Command+R 进入 恢复模式，在恢复模式中启动 terminal，输入：

```shell
csrutil disable
```

2. ### 重启之后，运行：

```shell
sudo mount -uw /	# 根目录挂载为可读写
```

3. ### link 如下几个目录

```shell

cd /System/Library/Frameworks/CoreFoundation.framework
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Headers Headers
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreFoundation.framework/Modules Modules


cd /System/Library/Frameworks/Security.framework
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Headers Headers
ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks/Security.framework/Modules Modules
```



**来源**: [Catalina macOS 10.15 下 CGO 不能运行的解决方案](https://medium.com/@croath/catalina-macos-10-15-下-cgo-不能运行的解决方案-5b580140b45a)

