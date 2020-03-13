# mac 安装supervisor

## 安装

```shell
brew install supervisor
```

## 使用

To have launchd start supervisor now and restart at login:
  `brew services start supervisor`
Or, if you don't want/need a background service you can just run:
  `supervisord -c /usr/local/etc/supervisord.ini`