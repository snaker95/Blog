# error 进阶

对于 go开发者来说, error 并不陌生, 也使用的得心应手, 但是真的使用的姿势对吗? 这个还真的需要打个问号. 听了毛大的课(go 进阶训练营), 才对 error有了新的认识, 至少现在认为是正确的认识

## 吐槽点

- if err!= nil:  代码中到处是这样的判断.

## Error 是定义的接口

- `type error interface{Errror() string}`

## 标准库中的 errors

- 定义了 type errorString struct { s string }
- 为什么使用 struct 定义
  - struct 可以丰富内容 (自以为)
- 为什么errors.New()返回的是 errorString 的指针
  1. 避免 错误类型 使用 字符串 作为对比条件
  2. 通过指针, 确保统一错误(Sentinel Error)只有一个, 通过对比指针地址确保为同一错误类型
- 技巧: 
  - New() 技巧: **包名: errString**; 例如: `errors.New("bufio: 错误")`, 方便定位问题

## panic 和 error 的区别

- error 只是逻辑异常, 不影响代码正常运行, 向上返回给调用者来处理
- panic 意味着 fatal error (挂了); 并不是返回给调用者来解决 panic,  代码不能继续运行(不可恢复的程序错误)
  - panic - recover() 的目的:
    - 兜底作用, 放弃当次调用, 保证程序不退出运行, 可以继续被调用



## error 分类

### Sentinel Error

预定义的特定错误, 例如: EOF = errors.New("EOF")

**特点**

  - 不能灵活的进行错误处理策略, 必须使用 == 将结果与 预定义的值进行比较
  - 不能提供更多的上下文, 提供更多的信息提示; 如果被包装后, 则破坏预定义错误的值

**注意**

- err.Error() 是为了方便程序员使用排查问题、写入日志等等， 而不是给程序使用（比较字符串等等进行判断逻辑）

**问题**

- 返回这一类 error, 则需要暴露这一部分的API
- 会将 两个包之间创建依赖, 甚至多包之间形成 import loop

### Error types 自定义 error

自定义error struct , 可以自定义元素丰富内部的值

**问题**

* 与Sentinel Error 相同

### Opaque Error 不透明错误

不关心Error 内部, 只关心其错误, 即只判断 if err != nil

**思路**

- Assert errors for behaviour, not type

**实践**

- 通过行为判断错误
- 例子: 
> ```go
> type temporary {
> 	Temporary() bool
> }
> te, ok := err.(temporary)
> return ok && te.Temporary()
> ```


## 处理 error技巧

1. 正常代码非缩进, 即 将 error 进行优先判断 err != nil
2. 封装功能为模块 struct, error 在模块中处理, **这个很难理解**

### Wrap Error

堆栈错误, 获取报错链路

you should only handle errors once.

- 只操作一次
  - 或向上抛出错误 return err 或者其变形, 例如 
    - `return errors.Wrap(err, "xxxx");`   显示堆栈追踪
    - `return errors.WithMessage(err, "xxxx")`  仅添加提示信息
  - 或进行逻辑处理(log以及其他...), 之后 `return nil`

### Wrap 包装

会将堆栈信息包装在内, 通过

  1. `fmt.Printf("%T %v\n", errors.Cause(err), errors.Cause(err))` // 显示 原始错误的类型 和 原始错误的描述
  2. `fmt.Printf("%+v", err) `显示信息, 包括堆栈信息和错误信息

**注意:**

- Wrap 尽量/ 最好 只在最底层使用, 否则, 堆栈信息成片重复出现, 不利于排查问题 (在业务代码中使用)
- 上层追加错误信息注释时, 和通过 WithMassage() 方法
- 使用 WithMassage()包装后, 如果使用最原始error, 则使用 errors.Cause(err)即可获得 

## Unwrap 

通过实现Unwrap()方法来实现 获取底层错误根因

#### Is(等值判断) 与  As (类型转换)

- Is: 与 err == ErroNotFound 类似, **递归等值判断**
  - 更高级: 会调用 Unwrap 递归展出下一层错误根因, 判断是否目标错误根因一致
- As: 类似断言 if e,ok := err.(*QueryError), **递归类型转换**
  - 更高级: 会调用 Unwrap 递归展出下一层错误根因, 进行进一步断言

## Go 1.13新特性

### Error扩展

- **Unwrap**
- **Is**: 可以通过自定义的 Error 来进一步扩展
- **As**: 可以通过自定义的 Error 来进一步扩展



## 总结使用 error的思路

1. 理清 error 和 log 的关系
   * log 是用来记录日志的, 方便程序员排查问题
   * error 是用来记录当前出现异常的提示信息, 方便定位问题
   * 将 log 和 error 结合后:

     发生error的时候, 在大多数的程序中, 大多数在 `return err` 的地方都会打印 log; **问题来了:**
     1. 当方法**调用层级比较多**的时候, 逐级 return err 并 打印 log, 造成重复性的无用 log, 浪费空间, 增加排查成本

2. error 的使用思路

   1. **errors.Wrap**: 合理利用 `errors.Wrap`, 合理表现在

      1. 整个 return 链路上仅使用一次
      2. 使用的节点一般在业务代码的底层, 即产生 err 的源头

   2. `errors.WithMessage`: 非 err 源头可通过 `errors.WithMessage`添加更多上下文, 便于记录 return error 的整个链路

   3. `errors.Is`: 进行 err 等值判断时, 避免使用 `if err := XX.ErrXXX`, 应当改为 `if errors.Is(err, XX.ErrXX)`

   4. `errors.As`: 对指定 error 类型做进一步处理时, 使用 

      ```go
      var myErr *MySelfError
      if errors.As(err,&myErr) {
        // 符合 myErr, 做处理
      }
      ```

      

3. 封装自定义的 error 需要注意
   1. 