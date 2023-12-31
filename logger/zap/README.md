# Introduction

官网：[zap log](https://github.com/uber-go/zap)  

Blazing fast, structured, leveled logging in Go.

# Features

- 高性能

# Usage

- SugaredLogger: 
  - 提供结构化和`printf`的日志输出格式，比其他结构化日志包快4-5倍。
  - 适用于性能不严苛的上下文场景中。

```go
log.SugaredLogger.Infow("failed to fetch URL",
	// Structured context as loosely typed key-value pairs.
	"url", "example.com",
	"attempt", 3,
	"backoff", time.Second)
```

- Logger：
  - 提供更高性能，比SugaredLogger性能更快，适用于性能要求严苛的上下文场景。
  - 只提供结构化的格式，需要显示声明结构化`zap.String`且没有`printf`的格式。

```go
log.Logger.Info("failed to fetch URL",
	// Structured context as strongly typed Field values.
	zap.String("url", "example.com"),
	zap.Int("attempt", 3),
	zap.Duration("backoff", time.Second),
)
```

# Example

```go
package zap

import (
	"errors"
	"testing"
	"time"

	log "github.com/huweihuang/golib/logger/zap"
	"go.uber.org/zap"
)

func TestZap(t *testing.T) {
	c := log.New()
	c.SetDivision("size")  // 设置归档方式，"time"时间归档 "size" 文件大小归档，文件大小等可以在配置文件配置
	c.SetTimeUnit(log.Day) // 时间归档 可以设置切割单位
	c.SetEncoding("json")  // 输出格式 "json" 或者 "console"

	c.SetInfoFile("./log/zap.log")        // 设置日志文件
	c.SetErrorFile("./log/zap.error.log") // 设置error日志文件
	c.SetLogLevel("debug")
	c.InitLogger()
	PrintLog()
}

func TestZapLogByConfig(t *testing.T) {
	c := log.NewFromYaml("config.yaml")
	c.InitLogger()

	PrintLog()
}

func PrintLog() {
	// SugaredLogger
	log.SugaredLogger.Info("info level test")
	log.SugaredLogger.Error("error level test")
	log.SugaredLogger.Warn("warn level test")
	log.SugaredLogger.Debug("debug level test")

	log.SugaredLogger.Infof("info level test: %s", "111")
	log.SugaredLogger.Errorf("error level test: %s", "111")
	log.SugaredLogger.Warnf("warn level test: %s", "111")
	log.SugaredLogger.Debugf("debug level test: %s", "111")

	log.SugaredLogger.Infow("failed to fetch URL",
		// Structured context as loosely typed key-value pairs.
		"url", "example.com",
		"attempt", 3,
		"backoff", time.Second)

	// Logger
	log.Logger.Info("failed to fetch URL",
		// Structured context as strongly typed Field values.
		zap.String("url", "example.com"),
		zap.Int("attempt", 3),
		zap.Duration("backoff", time.Second),
	)
	log.Info("this is a log", log.With("Trace", "12345677"))
	log.Info("this is a log", log.WithError(errors.New("this is a new error")))
}
```
