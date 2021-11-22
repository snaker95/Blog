# 服务初始化

[TOC]

## 基本思路

* 统一初始化文件配置, 故将配置全部放在 config 目录下, 使用 xxx.json 文件进行配置, 实现不同环境加载不同的配置文件
* 通过 init-config.go 进行加载 json 文件. 并实现统一的初始化方法. 供 main.go 使用

## 实现

### 加载配置文件

```go
package config

import (
	"encoding/json"
	"fmt"
	"github.com/sirupsen/logrus"
	"io/ioutil"
	"sync"
)

type EnvLevel string

const (
	Dev   EnvLevel = "dev"
	Stage EnvLevel = "stage"
	Pre   EnvLevel = "pre"
	Prod  EnvLevel = "prod"
)

// GlobalConfig 全局配置
type GlobalConfig struct {
	Logger        *Logger       `json:"logger"`          // 日志配置
	Api           *ApiServer    `json:"api"`             // api 服务配置
	ApiSecret     ApiSecret     `json:"api_secret"`      // 接口验签配置
	ApiSkipSecret ApiSkipSecret `json:"api_skip_secret"` // 跳过api验签配置
	DBMaster      *DB           `json:"db_master"`       // 数据库配置
}

// Logger 日志配置体
type Logger struct {
	Level logrus.Level `json:"level"` // panic, fatal, error, warn, info, debug, trace
	Path  string       `json:"path"`
	File  string       `json:"file"`
}

// ApiServer 服务配置
type ApiServer struct {
	Host string `json:"host"`
	Port int    `json:"port"`
}

// ApiSecret 验签体
type ApiSecret map[string]string

// ApiSkipSecret 跳过验签 api 接口
type ApiSkipSecret map[string]bool

// DB 数据配置文件
type DB struct {
	Host       string `json:"host"`
	Port       int    `json:"port"`
	UserName   string `json:"user_name"`
	Password   string `json:"password"`
	SelectedDB string `json:"selected_db"`
}

var (
	globalConfig *GlobalConfig
	configMux    sync.Once
)

func Init(env EnvLevel) error {
	if err := initEnv(env); err != nil {
		fmt.Printf("加载配置文件失败: error = %s", err.Error())
		return err
	}
	// 初始化日志配置
	if err := initLog(GetLogConfig()); err != nil {
		fmt.Printf("初始化日志失败: error = %s", err.Error())
		return err
	}
	// 初始化数据库
	if err := initDBMaster(GetDBMasterConfig()); err != nil {
		fmt.Printf("初始化数据库失败: error = %s", err.Error())
		return err
	}
	return nil
}

// initEnv 初始化配置
func initEnv(env EnvLevel) (err error) {
	configMux.Do(func() {
		filePath := fmt.Sprintf("config/%s.json", env)
		var (
			config GlobalConfig
			file   []byte
		)
		file, err = ioutil.ReadFile(filePath)
		if err != nil {
			fmt.Println("配置文件读取错误,找不到配置文件", err)
			return
		}

		if err = json.Unmarshal(file, &config); err != nil {
			fmt.Println("配置文件读取失败", err)
			return
		}
		globalConfig = &config
	})
	return err
}

// GetGlobalConfig 全局配置
func GetGlobalConfig() *GlobalConfig {
	return globalConfig
}

// GetApiConfig api配置
func GetApiConfig() *ApiServer {
	return globalConfig.Api
}

// GetApiSecretConfig api验签配置
func GetApiSecretConfig() ApiSecret {
	return globalConfig.ApiSecret
}

// GetApiSkipSecretConfig 跳api验签配置
func GetApiSkipSecretConfig() ApiSkipSecret {
	return globalConfig.ApiSkipSecret
}

// GetLogConfig 日志配置
func GetLogConfig() *Logger {
	return globalConfig.Logger
}

// GetDBMasterConfig 数据库配置配置
func GetDBMasterConfig() *DB {
	return globalConfig.DBMaster
}
```

### 初始化数据库

```go
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

// initDBMaster 初始化数据库连接
func initDBMaster(dbConfig *DB) error {
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

### 初始化日志 handle

```go
package config

import (
	"github.com/BurntSushi/toml"
	"github.com/sirupsen/logrus"
	"log"
	"sync"
	"time"
)

type EnvLevel string

const (
	Dev   EnvLevel = "dev"
	Stage EnvLevel = "stage"
	Pre   EnvLevel = "pre"
	Prod  EnvLevel = "prod"
)

var (
	ApiSecretMap     = make(ApiSecret)
	ApiSkipSecretMap = make(ApiSkipSecret)
)

// GlobalConfig 全局配置
type GlobalConfig struct {
	Logger        *Logger         `json:"logger" toml:"logger"`                   // 日志配置
	ApiServer     *ApiServer      `json:"api_server" toml:"api_server"`           // api 服务配置
	ApiSecret     *ApiSecrets     `json:"api_secret" toml:"api_secret"`           // 接口验签配置
	ApiSkipSecret *ApiSkipSecrets `json:"api_skip_secret" toml:"api_skip_secret"` // 跳过api验签配置
	DBMaster      *DB             `json:"db_master" toml:"db_master"`             // 数据库配置
	Gin           *Gin            `json:"gin" toml:"gin"`                         // gin 配置
}

// Logger 日志配置体
type Logger struct {
	Level logrus.Level `json:"level"` // panic, fatal, coderror, warn, info, debug, trace
	Path  string       `json:"path"`
	File  string       `json:"file"`
}

// ApiServer 服务配置
type ApiServer struct {
	Host           string        `json:"host" toml:"host"`
	Port           int           `json:"port" toml:"port"`
	ReadTimeout    time.Duration `json:"read_timeout" toml:"read_timeout"`
	WriteTimeout   time.Duration `json:"write_timeout" toml:"write_timeout"`
	WaitTimeout    time.Duration `json:"wait_timeout" toml:"wait_timeout"`
	MaxHeaderBytes int           `json:"max_header_bytes" toml:"max_header_bytes"`
}

// ApiSecret 验签体
type ApiSecret map[string]string
type ApiSecrets struct {
	Keys [][]string `json:"keys" toml:"keys"`
}

// ApiSkipSecret 跳过验签 api 接口
type ApiSkipSecret map[string]bool
type ApiSkipSecrets struct {
	Api []string `json:"api" toml:"api"`
}

// DB 数据配置文件
type DB struct {
	Host       string `json:"host" toml:"host"`
	Port       int    `json:"port" toml:"port"`
	UserName   string `json:"user_name" toml:"user_name"`
	Password   string `json:"password" toml:"password"`
	SelectedDB string `json:"selected_db" toml:"selected_db"`
}

// gin 配置
type Gin struct {
	Mode string `json:"mode" toml:"mode"` // gin 配置, debug or release
}

var (
	globalConfig *GlobalConfig
	configMux    sync.Once
)

func Init(conf string) error {
	if err := initEnv(conf); err != nil {
		log.Printf("加载配置文件失败: coderror = %s", err.Error())
		return err
	}
	return nil
}

// initEnv 初始化配置
func initEnv(filePath string) (err error) {
	configMux.Do(func() {
		var (
			config GlobalConfig
		)
		if _, err = toml.DecodeFile(filePath, &config); err != nil {
			log.Println("配置文件读取失败", err)
			return
		}
		globalConfig = &config
	})
	return err
}

// GetGlobalConfig 全局配置
func GetGlobalConfig() *GlobalConfig {
	return globalConfig
}

// GetApiConfig api配置
func GetApiConfig() *ApiServer {
	return globalConfig.ApiServer
}

// GetApiSecretConfig api验签配置
func GetApiSecretConfig() ApiSecret {
	var mutex sync.Mutex
	mutex.Lock()
	defer mutex.Unlock()
	if len(ApiSecretMap) > 0 {
		return ApiSecretMap
	}
	for i := range globalConfig.ApiSecret.Keys {
		if key := globalConfig.ApiSecret.Keys[i]; len(key) >= 2 {
			ApiSecretMap[key[0]] = key[1]
		}
	}
	return ApiSecretMap
}

// GetApiSkipSecretConfig 跳api验签配置
func GetApiSkipSecretConfig() ApiSkipSecret {
	var mutex sync.Mutex
	mutex.Lock()
	defer mutex.Unlock()
	if len(ApiSkipSecretMap) > 0 {
		return ApiSkipSecretMap
	}
	for i := range globalConfig.ApiSkipSecret.Api {
		ApiSkipSecretMap[globalConfig.ApiSkipSecret.Api[i]] = true
	}
	return ApiSkipSecretMap
}

// GetLogConfig 日志配置
func GetLogConfig() *Logger {
	return globalConfig.Logger
}

// GetDBMasterConfig 数据库配置配置
func GetDBMasterConfig() *DB {
	return globalConfig.DBMaster
}

// GetGinConfig gin配置
func GetGinConfig() *Gin {
	return globalConfig.Gin
}
```

