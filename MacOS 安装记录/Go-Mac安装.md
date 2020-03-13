## Go开发环境

```shell
brew install go

vim ~/.bash_profile

# 添加
export GOPATH=~/Develop/Go # Go程序的目录, 不要设置为GO安装目录
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOBIN

# 使立即生效
source ~/.bash_profile
```

## 安装包管理工具glipe

```shell
cd ~/Develop/Go/src # go程序的src目录

go get github.com/Masterminds/glide

go install github.com/Masterminds/glide

# 校验
glide
# 若校验失败
source ~/.bash_profile
```

