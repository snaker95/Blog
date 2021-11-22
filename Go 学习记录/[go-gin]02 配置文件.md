# TOML配置处理

## 配置工具的选择

但我们又遇到了一个问题，一个项目通常是有很多配置的，比如PHP的php.ini文件、Nginx的server.conf文件，那么Golang的项目又适合使用怎样的配置文件呢？

其实现在我们有很多选择，比如 JSON文件、INI文件、YAML文件和TOML文件等等。

其中这些文件，对应的Golang处理库如下：

- [encoding/json](https://godoc.org/encoding/json) -- 标准库中的包，可以处理JSON配置文件，缺点是不能加注释
- [gcfg](https://github.com/go-gcfg/gcfg) -- 处理INI配置文件
- [toml](https://github.com/BurntSushi/toml) -- 处理TOML配置文件
- [viper](https://github.com/spf13/viper) -- 处理JSON, TOML, YAML, HCL以及Java properties配置文件

其实关于怎么选择可以看看stackoverflow上的问题[How to handle configuration in Go](https://stackoverflow.com/questions/16465705/how-to-handle-configuration-in-go)。

## toml的使用

我根据自己的喜好选了toml，下面就来说下toml。

先来看一个TOML文件的例子：

```
# This is a TOML document.

title = "TOML Example"

[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T07:32:00-08:00 # First class dates

[database]
server = "192.168.1.1"
ports = [ 8001, 8001, 8002 ]
connection_max = 5000
enabled = true

[servers]

  # Indentation (tabs and/or spaces) is allowed but not required
  [servers.alpha]
  ip = "10.0.0.1"
  dc = "eqdc10"

  [servers.beta]
  ip = "10.0.0.2"
  dc = "eqdc10"

[clients]
data = [ ["gamma", "delta"], [1, 2] ]

# Line breaks are OK when inside arrays
hosts = [
  "alpha",
  "omega"
]
```

大家可以看到这里的格式非常灵活，可以是数字、字符串、布尔等简单类型，也可以是数组、map等等复杂的类型。

关于具体的TOML语言的解说大家查看文档 [toml-lang/toml](https://github.com/toml-lang/toml)

下面我们再来说一下，具体的Golang代码中如何使用

我们基于上面的配置文件来定义Golang中配置的struct，如下：

```
type tomlConfig struct {
	Title string
	Owner ownerInfo
	DB database `toml:"database"`
	Servers map[string]server
	Clients clients
}

type ownerInfo struct {
	Name string
	Org string `toml:"organization"`
	Bio string
	DOB time.Time
}

type database struct {
	Server string
	Ports []int
	ConnMax int `toml:"connection_max"`
	Enabled bool
}

type server struct {
	IP string
	DC string
}

type clients struct {
	Data [][]interface{}
	Hosts []string
}
```

这一些都定义好之后，我们只需要将文件配置中的内容转成Golang中可用的struct实例即可，代码如下：

```
var config tomlConfig
filePath := "/your/path/config.toml"
if _, err := toml.DecodeFile(filePath, &config); err != nil {
	panic(err)
}
```

这样我们拿到的config就是拥有TOML文件内容的tomlConfig的实例，可以直接使用。

## 配置的单例模式

通常来说，在一个项目中，配置文件只需要解析一次，所以可以使用单例模式包一下config的解析。

代码如下：

```
package config

var (
	cfg * tomlConfig
	once sync.Once
)

func Config() *tomlConfig {
	once.Do(func() {
		filePath, err := filepath.Abs("./ch3/config.toml")
		if err != nil {
			panic(err)
		}
		fmt.Printf("parse toml file once. filePath: %s\n", filePath)
		if _ , err := toml.DecodeFile(filePath, &cfg); err != nil {
			panic(err)
		}
	})
	return cfg
}
```

这里我们使用了sync.Once的Do方法，Do方法当且仅当第一次被调用时才执行函数。

如果once.Do(f)被多次调用，只有第一次调用会执行f，即使f每次调用Do 提供的f值不同。需要给每个要执行仅一次的函数都建立一个Once类型的实例。

这样我们就保证了tomlConfig对象是一个单例模式，只需要解析一次，可以在任何地方调用。调用例子如下：

```
// 配置中DB的IP
fmt.Println(config.Config().DB.Server)
// 配置中Owner的名字
fmt.Println(config.Config().Owner.Name)
```

## 配置的更新

如果我们的项目是一个常驻的项目（比如http server），我们会希望能够提供更新配置的功能，平滑的替换掉配置，不需要重启项目。

其实思路很想简单，我们只需要起一个协程，监视我们定义好的信号，如果接收到信号就重新加载配置。

下面我们来写下，更新配置的代码：

```
	s := make(chan os.Signal, 1)
	signal.Notify(s, syscall.SIGUSR1)
	go func() {
		for {
			<-s
			config.ReloadConfig()
			log.Println("Reloaded config")
		}
	}()
```

我们监视了syscall.SIGUSR1信号，其值是30，接收到信号就执行config.ReloadConfig()方法。

再来看下config中方法变动：

```
func Config() *tomlConfig {
	once.Do(ReloadConfig)
	cfgLock.RLock()
	defer cfgLock.RUnlock()
	return cfg
}

func ReloadConfig() {
	filePath, err := filepath.Abs("./ch3/config.toml")
	if err != nil {
		panic(err)
	}
	fmt.Printf("parse toml file once. filePath: %s\n", filePath)
	config := new(tomlConfig)
	if _ , err := toml.DecodeFile(filePath, config); err != nil {
		panic(err)
	}
	cfgLock.Lock()
	defer cfgLock.Unlock()
	cfg = config
}
```

原来加载配置的代码放到ReloadConfig方法中去了，还在给变量cfg赋值的时候加了读写锁，以保证安全。在Config方法中获取cfg的时候加了读锁，防止在读的时候，也在写入，导致配置错乱。

启动server之后，可以通过如下shell命令更新配置

```
kill -SIGUSR1 6666
```

其中的6666是go server的进程号。执行这条命令之后，会向go server发送syscall.SIGUSR1的信号，从而触发更新配置的动作。

## POSIX信号

这边顺便列一下POSIX中定义的信号：

Linux 使用34-64信号用作实时系统中。

命令 man 7 signal 提供了官方的信号介绍。

#### 在POSIX.1-1990标准中定义的信号列表：

| 信号    | 值       | 动作 | 说明                                                         |
| ------- | -------- | ---- | ------------------------------------------------------------ |
| SIGHUP  | 1        | Term | 终端控制进程结束(终端连接断开)                               |
| SIGINT  | 2        | Term | 用户发送INTR字符(Ctrl+C)触发                                 |
| SIGQUIT | 3        | Core | 用户发送QUIT字符(Ctrl+/)触发                                 |
| SIGILL  | 4        | Core | 非法指令(程序错误、试图执行数据段、栈溢出等)                 |
| SIGABRT | 6        | Core | 调用abort函数触发                                            |
| SIGFPE  | 8        | Core | 算术运行错误(浮点运算错误、除数为零等)                       |
| SIGKILL | 9        | Term | 无条件结束程序(不能被捕获、阻塞或忽略)                       |
| SIGSEGV | 11       | Core | 无效内存引用(试图访问不属于自己的内存空间、对只读内存空间进行写操作) |
| SIGPIPE | 13       | Term | 消息管道损坏(FIFO/Socket通信时，管道未打开而进行写操作)      |
| SIGALRM | 14       | Term | 时钟定时信号                                                 |
| SIGTERM | 15       | Term | 结束程序(可以被捕获、阻塞或忽略)                             |
| SIGUSR1 | 30,10,16 | Term | 用户保留                                                     |
| SIGUSR2 | 31,12,17 | Term | 用户保留                                                     |
| SIGCHLD | 20,17,18 | Ign  | 子进程结束(由父进程接收)                                     |
| SIGCONT | 19,18,25 | Cont | 继续执行已经停止的进程(不能被阻塞)                           |
| SIGSTOP | 17,19,23 | Stop | 停止进程(不能被捕获、阻塞或忽略)                             |
| SIGTSTP | 18,20,24 | Stop | 停止进程(可以被捕获、阻塞或忽略)                             |
| SIGTTIN | 21,21,26 | Stop | 后台程序从终端中读取数据时触发                               |
| SIGTTOU | 22,22,27 | Stop | 后台程序向终端中写数据时触发                                 |

#### 在SUSv2和POSIX.1-2001标准中的信号列表:

| 信号      | 值       | 动作 | 说明                                              |
| --------- | -------- | ---- | ------------------------------------------------- |
| SIGTRAP   | 5        | Core | Trap指令触发(如断点，在调试器中使用)              |
| SIGBUS    | 0,7,10   | Core | 非法地址(内存地址对齐错误)                        |
| SIGPOLL   |          | Term | Pollable event (Sys V). Synonym for SIGIO         |
| SIGPROF   | 27,27,29 | Term | 性能时钟信号(包含系统调用时间和进程占用CPU的时间) |
| SIGSYS    | 12,31,12 | Core | 无效的系统调用(SVr4)                              |
| SIGURG    | 16,23,21 | Ign  | 有紧急数据到达Socket(4.2BSD)                      |
| SIGVTALRM | 26,26,28 | Term | 虚拟时钟信号(进程占用CPU的时间)(4.2BSD)           |
| SIGXCPU   | 24,24,30 | Core | 超过CPU时间资源限制(4.2BSD)                       |
| SIGXFSZ   | 25,25,31 | Core | 超过文件大小资源限制(4.2BSD)                      |

转载: https://www.cnblogs.com/CraryPrimitiveMan/p/7928647.html

