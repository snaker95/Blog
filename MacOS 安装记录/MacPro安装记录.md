# mac pro 安装记录

## brew 安装

```shell
# brew 安装
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# 替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
# 替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
# 应用生效
brew update

# 还原brew
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core 
brew update

# brew 缓存应用程序目录
~/Library/Caches/Homebrew


# 安装 zsh
brew install zsh
sh -c "$(curl -fsSl https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s `which zsh` # 设置more
# htop
brew install htop
brew cask install docker
# ### -------------- CASKS ------------------### #
# lrzsz 如何配置
brew install lrzsz
mkdir ~/Develop/GitHub
cd ~/Develop/GitHub && git clone https://github.com/aurora/iterm2-zmodem.git
# 打开iterm2 ------  同时按 command和,键 ------- Profiles ----------  Default ------- Advanced ------ Triggers的Edit按钮
# Regular expression: \*\*B0100
# Action:             Run Coprocess
# Parameters:         ~/Develop/GitHub/iterm2-zmodem/iterm2-zmodem sz

# Regular expression: \*\*B00000000000000
# Action:             Run Coprocess
# Parameters:         ~/Develop/GitHub/iterm2-zmodem/iterm2-zmodem rz
### -------------------- #### --------------------- ###
# ### -------------- CASKS ------------------ ### #
# ----------------- 终端类 ---------------------
# 安装iterm2 终端
brew cask install iterm2
# 安装windows远程工具 microsoft-remote-desktop
brew search microsoft-remote-desktop
brew cask install homebrew/cask-versions/microsoft-remote-desktop-beta
# 安装virtualbox
brew cask install virtualbox

# ----------------- 常用工具 ---------------------
# 安装qq / wechat
brew cask install qq
brew cask install wechat
# 安装 搜狗输入法, 注意: 下载完成后, 需要使用 open 命令开启安装
brew cask install sogouinput
# 安装 chrome
brew cask install google-chrome
# 安装网易云
brew cask install neteasemusic
# 喜马拉雅
brew cask install ximalaya
# 安装 iina(视频播放器)
brew cask install iina

# ----------------- 编辑器类 ---------------------
# 安装sublime-text 编辑器
brew cask install sublime-text
# 安装typora
brew cask install typora
# 安装 pycharm
brew cask install pycharm
brew cask install phpstorm
brew cask install intellij-idea
# vscode
brew cask install visual-studio-code

# ----------------- 数据库类 ---------------------
# 安装robomongo mongo客户端
brew cask install robomongo
# Mysql客户端
brew cask install sqlpro-for-mysql
# 安装 sequel-pro (数据库连接工具)
brew cask install sequel-pro
# redis 
brew search redis

# ----------------- 小工具类 ---------------------
# 安装switchhosts hosts
brew cask install switchhosts
# 安装postman
brew cask install postman
# 安装shadowsocksx
brew cask install shadowsocksx
# 下载工具 folx 
brew cask install folx
# 安装charles 抓包工具
brew cask install charles
# 安装snip截图
brew cask install snip
# gif录屏工具
brew cask install licecap
# 网络流量工具 istat-menus
brew cask install istat-menus

# ----------------- 思图类 ---------------------
# 安装xmind
brew cask install xmind
# draw导图
brew cask install drawio

# ----------------- 下载类 ---------------------
# 安装aria2gui 网盘下载
brew cask install aria2gui
# 安装坚果云
brew cask install nutstore
```

## Go语言

* 安装  [Go-Mac安装.md](Go-Mac安装.md) 



## docker 相关

```shell
# 安装MariaDB
docker run  --name MariaDB --restart=on-failure:10 -p 3307:3306 -v ~/Develop/Docker/mariaDB/data:/var/lib/mysql: -e MYSQL_ROOT_PASSWORD='root123' -d mariadb --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
# 安装 MySQL8
docker run  --name MySQL --restart=on-failure:10 -p 3306:3306 -v ~/Develop/Docker/MySQL8/data:/var/lib/mysql: -e MYSQL_ROOT_PASSWORD='root123'  -d mysql:latest --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

# redis
docker run --name Redis --restart=on-failure:10 -p 6379:6379 -v ~/Develop/Docker/Redis/data:/data -d redis redis-server --appendonly yes --requirepass "R3dis."

# 安装monogo
docker run --name Mongo --restart=on-failure:10 -p 27017:27017 -v ~/Develop/Docker/MongoDB/db:/data/db -d mongo

# 安装 RabbitMQ
docker run --name RabbitTest --restart=on-failure:10 --hostname rabbit-test -p 5672:5672 -p 15672:15672 -d rabbitmq:3.8-management

# 安装nginx - php7
docker run -v ~/Develop/WebServer/php7:/data:ro --restart=on-failure:10 --name centos7-php7 --link=MariaDB:mysqlhost --link=Redis:redishost -d snaker95/centos7-php7:v1

# php-fmp安装
docker run --name php-fpm5.6 -p 9000:9000 -v ~/Develop/WebServer/php:/var/www/html -d php:5.6-fpm

# Nginx
docker run --name nginx -v ~/Develop/WebServer:/usr/share/nginx/html -v ~/Develop/Docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/Develop/Docker/nginx/conf/conf.d:/etc/nginx/conf.d --restart=on-failure:10 --link php-fpm5.6 --link php-fpm7 -p 80:80 -d nginx


# idea-license-server
docker run --name idea_licence_server  --restart=on-failure:10 -p 1027:1017 -e USER="$USER" -d dominate/idea-license-server

docker run --name idea_licence_server  --restart=on-failure:10 -d -p 9999:8888 -e "r=http://idea.starter.com" -e "l=0.0.0.0:8888" ilanyu/golang-reverseproxy

```



## mac项目工具命令

```shell
# 清理内容和磁盘
sudo purge
```

## 其他工具

* CHM Reader

## 工具配置

* [iterm2配置](./iterm2配置.md)
* typora
* sublime
* pycharm [坚果云IDE中]
* phpstorm [坚果云IDE中], agent代理激活方法
* idea [坚果云IDE中]
* vscode
*  [Vim简单配置.md](../Vim/Vim简单配置.md) 

  

