# go Context

[TOC]

### 1. 初始context

比如一个网络请求Request，每个Request都需要开启一个goroutine做一些事情，这些goroutine又可能会开启其他的goroutine。所以我们需要一种可以跟踪goroutine的方案，才可以达到控制他们的目的，这就是go语言的Context，它就是goroutine的上下文

例

```go
func main() {
    ctx, cancel := context.WithCancel(context.Background())
    go func(ctx context.Context) {
        for {
            select {
            case <-ctx.Done():
                fmt.Println("监控退出，停止了...")
                return
            default:
                fmt.Println("goroutine监控中...")
                time.Sleep(2 * time.Second)
            }
        }
    }(ctx)

    time.Sleep(10 * time.Second)
    fmt.Println("可以了，通知监控停止")
    cancel()
    //为了检测监控过是否停止，如果没有监控输出，就表示停止了
    time.Sleep(5 * time.Second)

}
```

`context.Background()` 返回一个空的Context，这个空的Context一般用于整个Context树的根节点。然后我们使用`context.WithCancel(parent)` 函数，创建一个可取消的子Context，然后当作参数传给goroutine使用，这样就可以使用这个子Context跟踪这个goroutine

在goroutine中，使用select调用`<-ctx.Done()`判断是否要结束，如果接收到值的话，就可以返回结束goroutine。如果接收不到，就会继续进行监控。

调用取消函数就可以发出取消指令，然后监控goroutine就会收到信号，就会返回

## 2. Context接口

接口方法

```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)

    Done() <-chan struct{}

    Err() error

    Value(key interface{}) interface{}
}
```

- Deadline()(deadline time.Time,ok bool)：获取设置的截止时间。第一个返回的是截止时间，到了这个时间点，Context会自动发起取消请求。第二个返回值ok==false时，表示没有设置截止时间，需要手动调用取消函数进行取消
- Done() <-chan struct{}：返回一个只读的chan，类型为struct{}，在goruntine中，如果该方法返回的chan可以读取，则意味着parent context已经发起了取消请求，我们通过Done方法收到这个信号后，就应该做清理操作，然后退出goroutine并释放资源
- Err：返回取消的错误原因，因为什么Context被取消
- value：获取该Context上绑定的值，是一个键值对，所以要通过一个Key才可以获取对应的值，这个值是线程安全的

Context接口不需要我们自己实现，go内置已经实现了2个，我们代码中最开始都是以这两个内置作为最顶层的parent context，衍生出更多的子context

```go
var (
    background = new(emptyCtx)
    todo       = new(emptyCtx)
)

func Background() Context {
    return background
}

func TODO() Context {
    return todo
}
```

一个是Background，主要用于main函数 、初始化以及测试代码中，作为Context这个树结构的最顶层的Context，也就是根Context

一个是todo，它是目前还不知道具体的使用场景，如果我们不知道该使用什么Context的时候，可以使用这个

他们两个本质上都是emptyCtx结构体类型，是一个不可取消，没有设置截止时间，没有携带任何值的Context

```go
type emptyCtx int

func (*emptyCtx) Deadline() (deadline time.Time, ok bool) {
    return
}

func (*emptyCtx) Done() <-chan struct{} {
    return nil
}

func (*emptyCtx) Err() error {
    return nil
}

func (*emptyCtx) Value(key interface{}) interface{} {
    return nil
}
```

这就是emptyCtx实现Context接口的方法。

## 3. Context的继承衍生

有了如上的根Context，那么是如何衍生更多的子context呢？这就要靠Context包给我们提供的With系列的函数

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
func WithValue(parent Context, key, val interface{}) Context
```

这四个with函数，接收的都有一个parent参数，就是父Context，我们基于这个父Context创建出子Context。

通过这些函数，就创建了一个Context树，树的每个节点都可以有任意多个子节点，节点层级可以有任意多个

`WithCancel`函数，传递一个父Context作为参数，返回子Context，以及一个取消函数用来取消Context。 `WithDeadline`函数，和`WithCancel`差不多，它会多传递一个截止时间参数，意味着到了这个时间点，会自动取消Context，当然我们也可以不等到这个时候，可以提前通过取消函数进行取消。

`WithTimeout`和`WithDeadline`基本上一样，这个表示是超时自动取消，是多少时间后自动取消Context的意思。

`WithValue`函数和取消Context无关，它是为了生成一个绑定了一个键值对数据的Context，这个绑定的数据可以通过`Context.Value`方法访问到，后面我们会专门讲。`WithCancel` 函数，传递一个父Context作为参数，返回子Context，以及一个取消函数用于取消Context

前三个函数都返回一个取消函数CancelFunc,这时一个函数类型，它的定义非常简单

```go
type CancelFunc func()
```

该函数可以取消一个Context，以及这个节点Context下所有的Context，不管有多少层级

## 4. Context使用原则

1. 不要把Context放在结构体中，要以参数的方式传递
2. 以Context作为参数的函数方法，应该把Context作为第一个参数，放在第一位
3. 给一个函数方法传递Context的时候，不要传递nil，如果不知道传递什么，就使用context.TODO()
4. Context的Value相关方法应该传递必须的参数，不要什么数