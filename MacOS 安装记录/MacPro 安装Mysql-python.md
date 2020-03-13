1. 报错`fatal error: 'my_config.h' file not found`

```shell
brew install mysql
brew unlink mysql
brew install mysql-connector-c
sed -i -e 's/libs="$libs -l "/libs="$libs -lmysqlclient -lssl -lcrypto"/g' /usr/local/bin/mysql_config
pip install MySQL-python
brew unlink mysql-connector-c
brew link --overwrite mysql
```



2. 报错

   ```shell
   #        define SIZEOF_LONG             4
                       ^
       In file included from _mysql.c:44:
       /usr/local/include/my_config.h:179:9: warning: 'SIZEOF_TIME_T' macro redefined [-Wmacro-redefined]
       #define SIZEOF_TIME_T    8
             	....
   ```

   解决方案

```shell
sudo pip install MySQL-Python --global-option=build_ext --global-option="-I/usr/local/opt/openssl/include" --global-option="-L/usr/local/opt/openssl/lib"
```