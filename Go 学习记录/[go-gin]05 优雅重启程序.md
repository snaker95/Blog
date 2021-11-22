## 优雅重启程序



参考: [优雅的重启服务](https://www.bookstack.cn/read/eddycjy-go/gin-reload-http.md)

```go
    s := &http.Server{
        Addr:           fmt.Sprintf(":%d", setting.HTTPPort),
        Handler:        router,
        ReadTimeout:    setting.ReadTimeout,
        WriteTimeout:   setting.WriteTimeout,
        MaxHeaderBytes: 1 << 20,
    }
    go func() {
        if err := s.ListenAndServe(); err != nil {
            log.Printf("Listen: %s\n", err)
        }
    }()
    quit := make(chan os.Signal)
		signal.Notify(quit, os.Interrupt, os.Kill)
    <- quit
    log.Println("Shutdown Server ...")
    ctx, cancel := context.WithTimeout(context.Background(), 5 * time.Second)
    defer cancel()
    if err := s.Shutdown(ctx); err != nil {
        log.Fatal("Server Shutdown:", err)
    }
    log.Println("Server exiting")
}
```

