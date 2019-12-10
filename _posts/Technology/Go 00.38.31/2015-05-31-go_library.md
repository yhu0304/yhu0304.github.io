---

layout: post
title: Go 常用的一些库
category: 技术
tags: Go
keywords: Go

---

## 一 前言

* TOC
{:toc}

本文主要阐述一下golang中常用的库。

## 如何组织一个大项目的go 代码

![](/public/upload/go/go_module.png)

### 项目代码划分

[使用 Go 语言开发的一些经验（含代码示例）](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2651008064&idx=2&sn=cdc19d0db8decad85b671ba79fd2d1f5&chksm=bdbed4138ac95d05dbfd6672babba8e4d4a547d7845cd46b23fe3802dd5a1c49777b476fadd5&mpshare=1&scene=23&srcid=0708wchJyw4BGm9vtQxV8qaT%23rd) 要点如下

1. 可见性和代码划分

	* c++ 在类上，即哪怕在同一个代码文件中，仍然无法访问一个类的私有方法
	* java 是 类 + 包名
	* go 在包上，从其他包中引入的常量、变量、函数、结构体以及接口，都需要加上包的前缀来进行引用。Golang 也可以 dot import 来去掉这个前缀。不幸的是，这个做法并不常规，并且不被建议。

1. 假设有一个用户信息管理系统，直观感觉上的分包方式

	* 单一package
	* 按mvc划分，比如controller包、model包，缺点就是你使用 controller类时 就只得`controller.UserController`,controller 重复了
	* 按模块划分。比如`user/UserControler.go,user/User.go`，缺点就是使用User类时只得 `user.User`

2. 按依赖划分，即根包下 定义接口文件`servier.go`，包含User和UserController 接口定义，然后定义`postgresql/UserService.go` 或者`mysql/UserService.go`

github 也有一些demo 项目layout [golang-standards/project-layout](https://github.com/golang-standards/project-layout)

[作为一名Java程序员，我为什么不在生产项目中转向Go](http://www.infoq.com/cn/articles/why-not-go)

并发中处理的内容才是关键，新启一个线程或者协程才是万里长城的第一步，如果其中的业务逻辑有10个分支，还要多次访问数据库并调用远程服务，那无论用什么语言都白搭。所以在业务逻辑复杂的情况下，语言的差异并不会太明显，至少在Java和Go的对比下不明显	
[Organizing Go source code part 2](http://neurocline.github.io/dev/2016/02/01/organizing-go-source-code.html) 未读

## Go中的依赖注入

[Go中的依赖注入](https://www.jianshu.com/p/cb3682ad34a7) 推荐使用 [uber-go/dig](https://github.com/uber-go/dig) 
A reflection based dependency injection toolkit for Go.

依赖注入是你的组件（比如go语言中的structs）在创建时应该接收它的依赖关系。PS：这个理念在java、spring 已经普及多年。这与在初始化期间构建其自己的依赖关系的组件的相关反模式相反。

**设计模式分为创建、结构和行为三大类，如果自己构造依赖关系， 则创建 与 行为 两个目的的代码容易耦合在一起， 代码较长，给理解造成困难。**

## 二 日志

golang中涉及到日志的库有很多，除了golang自带的log外，还有glog和log4go等，不过由于bug、更新缓慢和功能不强等原因，笔者推荐使用seelog。

### 2.1 安装

    $ go get github.com/cihub/seelog
    
### 2.1 使用

    package main
    import (
    	"fmt"
    	log "github.com/cihub/seelog"
    )
    func main() {
        // xml格式的字符串，xml配置了如何输出日志
    	testConfig := `
        <seelog type="sync">
            // 配置输出项，本例表示同时输出到控制台和日志文件中
        	<outputs formatid="main">
        		<console/>
        		<file path="log.log"/>
        	</outputs>
        	<formats>
        	    // 日志格式，outputs中的输出项可以通过id引用该格式
        		<format id="main" format="[%LEVEL] [%Time] [%FuncShort @ %File.%Line] %Msg%n"/>
        	</formats>
        </seelog>`   
    
        // 根据配置信息生成logger（应该是配置信息的对象表示）
    	logger, err := log.LoggerFromConfigAsBytes([]byte(testConfig))
    	if err != nil {
    		fmt.Println(err)
    	}
    	// 应该是配置日志组件加载配置信息，然后输出函数比如Info在输出时，会加载该配置
    	log.ReplaceLogger(logger)
    	log.Info("Hello from Seelog!")
    }
    
当然，也可以将配置项专门写入到一个文件中，使用`log.LoggerFromConfigAsFile("xx.xml")`加载。

## 三 json字符串与结构体的转换

Go中的json处理，跟结构体是密切相关的，一般要为json字符串建好相对应的struct。

如果只是想获取json串中某个key的值，可以使用`github.com/bitly/go-simplejson`

    js, err := simplejson.NewJson([]byte(inputStr))
	path := js.Get("path").MustString()
	
如果知道json字符串对应结构体（该结构体可能会嵌套），则可以使用golang自带的encoding/json包。

    type name struct {
    	FN string `json:"fn"`
    	LN string `json:"ln"`
    }
    type user struct {
    	Id       int64  `json:"id"`            // 指定转换为字符串后，该字段显示的key值
    	Username string `json:"username"`
    	Password string `json:"-"`            
    	N        name   `json:"name"`
    }
    func main() {
    	n := name{
    		FN: "li",
    		LN: "qk",
    	}
    	u := user{
    		Id:       1,
    		Username: "lqk",
    		Password: "123456",
    		N:        n,
    	}
    	// 根据结构体生成字符串
    	b, _ := json.Marshal(u)
    	
        jsonStr := string(b)
    	fmt.Println(jsonStr)
    	
        var u2 user
        // 根据字符串生成结构体
        err := json.Unmarshal([]byte(jsonStr), &u2)
    }

## 读写锁

    package main
    import (
    	"errors"
    	"fmt"
    	"sync"
    )
    var (
    	pcodes         = make(map[string]string)
    	mutex          sync.RWMutex
    	ErrKeyNotFound = errors.New("Key not found in cache")
    )
    func Add(address, postcode string) {
        // 写入的时候要完全上锁
    	mutex.Lock()
    	pcodes[address] = postcode
    	mutex.Unlock()
    }
    func Value(address string) (string, error) {
        // 读取的时候，只用读锁就可以
    	mutex.RLock()
    	pcode, ok := pcodes[address]
    	mutex.RUnlock()
    	if !ok {
    		return "", ErrKeyNotFound
    	}
    	return pcode, nil
    }
    func main() {
    	Add("henan", "453600")
    	v, err := Value("henan")
    	if err == nil {
    		fmt.Println(v)
    	}
    }
    
## command line application

go 可执行文件没有复杂的依赖（java依赖jvm、python 依赖python库），特别适合做一些命令行工具

大概的套路都是

1. 定义一个Command对象
2. Command 对象一般有一个 name，多个flag（全写和简写） 以及一个处理函数

### [urfave/cli](https://github.com/urfave/cli)

cli is a simple, fast, and fun package for building command line apps in Go. The goal is to enable developers to write fast and distributable command line applications in an expressive way.

Things like generating help text and parsing command flags/options should not hinder productivity when writing a command line app.This is where cli comes into play. cli makes command line programming fun, organized, and expressive!

### [spf13/cobra](https://github.com/spf13/cobra) 

这个库牛就牛在k8s 用的也是它

The best applications will read like sentences when used(命令执行起来应该像句子一样). Users will know how to use the application because they will natively understand how to use it.

The pattern to follow is `APPNAME VERB NOUN --ADJECTIVE`. or `APPNAME COMMAND ARG --FLAG`

A flag is a way to modify the behavior of a command 这句说的很有感觉

### command line Application 的目录结构

cobra 推荐

  	appName/
    	cmd/
        	add.go
        	your.go
        	commands.go
        	here.go
      	main.go
      	
typically the main.go file is very bare. It serves one purpose: initializing.

## http

go语言中的`github.com/gorilla`可以方便的进行http url 到处理方法的dispatch，`github.com/urfave/cli` 则实现了用户输入命令到处理方法的dispatch。