## 数据库连接及配置

[TOC]

### 数据 ORM 选用 `gorm`

* 初始化数据库连接 (只在启动服务初始化)

  * ```go
    package config
    
    import (
    	"fmt"
    	"gorm.io/driver/mysql"
    	"gorm.io/gorm"
    	"sync"
    )
    
    var (
    	db         *gorm.DB
    	initDBOnce sync.Once
    )
    
    // InitDBMaster 初始化数据库连接
    func InitDBMaster(dbConfig *DB) error {
    	var err error
    	initDBOnce.Do(func() {
    		dsn := fmt.Sprintf(
    			"%s:%s@tcp(%s:%d)/%s?charset=utf8mb4&parseTime=True&loc=Local",
    			dbConfig.UserName,
    			dbConfig.Password,
    			dbConfig.Host,
    			dbConfig.Port,
    			dbConfig.SelectedDB,
    		)
    		// 连接
    		db, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
    		// 数据库优化 交由 DBA 维护
    	})
    	return err
    }
    
    // GetDBMaster 获取数据库 connection
    func GetDBMaster() *gorm.DB {
    	return db
    }
    ```

  * 使用方法

    * 详见 [Gorm Doc](https://gorm.io/docs/index.html)

### 疑问破解

#### 疑问一: 初始化后, 连接密码被修改, 是否影响查询

* 实验: 
  * 第一步: 配置正确数据库用户名和密码, 启动服务
    * `go run main.go`
  * 第二步: 通过终端修改该用户名的密码, 请求服务, 发现**数据库连接正常, 可以正常使用**
    *  `alter user 'error_code_go_user'@'%' identified with mysql_native_password by 'error_code_go_user.';`
  * 第三步: 通过终端, 查看连接数 , 此时发现**存在 Sleep 的该用户的连接**
    * `show full processlist; `
  * 第四步: 通过终端, kill 掉连接, 再重新请求, 发现**数据库连接失败, 报用户名密码错误**
    * `kill 8002`
* 结论:
  * 一经初始化, 在 Mysql 数据库中建立长链接, 此时修改密码无法达到断掉长链接的作用, 需要将这些长链接kill 掉

#### 疑问二: 初始化后, 如果将长链接全部关闭, 是否会自动重新生成长链接, 保证数据库正常链接

* 实验:
  * 第一步: 配置正确数据库用户名和密码, 启动服务
    * `go run main.go`
  * 第二步: 通过终端, 查看连接数
    * `show full processlist; `
  * 第三步: 通过终端, kill 掉连接, 再重新请求, 发现**自动生成长链接, 数据库正常使用**
    * `kill 8002`
* 结论: 
  * 一次初始化链接, 不用担心长链接被全部关闭造成数据库异常

#### 疑问三: 多次初始化, 会出现什么情况?

* 实验:

  * 在初始化时, 多次调用数据库链接

    * ```go
      s := make([]int, 100, 100)
      		for range s {
      			go func() {
      				time.Sleep(time.Second)
      				db, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
      			}()
      		}
      ```

* 结论:

  * 会生成 N倍的长链接;