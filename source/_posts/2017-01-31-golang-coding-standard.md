---
title: Golang 编码规范
date: 2017-01-31 21:25:36
tags: [Golang, standard]
categories:
---


# 注释
在编码的阶段同时写好变量、函数、包等的注释.  
注释必须是完整的句子, 以需要注释的内容作为开头, 句点作为结尾.  
程序中每一个被导出的(大写的)名字, 都应该有一个文档注释.  

## 包注释
每个程序包都应该有一个包注释, 一个位于 package 子句之前的块注释或行注释.  
包如果有多个 go 文件, 包注释只需要出现在一个 go 文件中即可.

```go
// Package regexp implements a simple library for regular expressions.
package regexp
```

<!-- more -->

## 可导出类型
第一条语句应该为一条概括语句, 并且使用被声明的名字作为开头.

```go
// Compile parses a regular expression and returns, if successful, a Regexp object  
// that can be used to match against text.
func Compile(str string) (regexp *Regexp, err error) {
    // implement code
}
```

<br/>

# 命名
使用短命名, 长名字并不会自动使得事物更易读, 文档注释会比格外长的名字更有用.

## 包名
包名应该为小写单词, 不要使用下划线或者混合大小写.


## 接口名
单个函数的接口名以 "er" 作为后缀, 如 Reader, Writer.  
接口的实现则去掉 "er".

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

两个函数的接口名, 综合两个函数名:

```go
type WriteFlusher interface {
    Write([]byte) (int, error)
    Flush() error
}
```

而三个以上函数的接口名, 类似于结构体名:

```go
type Car interface {
    Start([]byte)
    Stop() error
    Recover()
}
```


## 混合大小写
采用驼峰式命名  
MixedCaps 大写开头, 可导出  
mixedCaps 小写开头, 不可导出


## 函数
函数采用命名的多值返回  
传入变量和返回变量以小写字母开头

```go
func nextInt(b []byte, pos int) (value, nextPos, int) {
    // implement code
}
```

<br/>

# 控制结构

## if
if 接受初始化语句, 约定如下方式建立局部变量:

```go
if err := file.Chmod(0644); err != nil {
    return err
}
```


## for
采用短声明建立局部变量:

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```


## range
如果只需要第一项(key), 就丢弃第二个:

```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

如果只需要第二项, 则把第一项置为下划线:

```go
sum := 0
for _, value := range array {
    sum += value
}
```


## return
尽早 return. 一旦有错误发生, 马上返回.

```go
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}

codeUsing(f, d)
```

<br/>

# 错误处理
error 作为函数的值返回, 必须对 error 进行处理.  
错误描述如果是英文, 必须为小写, 并且不需要标点. 结尾采用独立的错误流进行处理.

不要像下面这样做:

```go
if err != nil {
    // error handling
} else {
    // normal code
}
```

而应该采用以下的方式:

```go
if err != nil {
    // error handling
    return // or continue, etc.
}
// normal code
```

如果返回值需要初始化, 则采用下面的方式:

```go
x, err := f()
if err != nil {
    // error handling
    return
}
// use x
```

<br/>

# panic
尽量不要使用 panic, 除非你知道自己在做什么.

<br/>

# import
对 import 的包进行分组管理, 而且遵循 `标准库`、`第三方库`、`自库` 的分组顺序.

```go
package main

import (
    "fmt"
    "hash/adler32"
    "os"

    "code.google.com/p/x/y"
    "github.com/foo/bar"

    "appengine/user"
    "appengine/foo"
)
```

<br/>

# 缩写
采用全部大写, 或者全部小写来表示缩写单词.  
比如对于 url 这个单词, 不要使用 `UrlPony`, 而要使用 `urlPony` 或者 `URLPony`.

<br/>

# 参数传递
- 对于少量数据, 不要传递指针.
- 对于大量数据的 `struct`, 可以考虑使用指针.
- 传入参数是 `map`、`slice`、`chan`, 不要传递指针(因为这3种为引用类型).

<br/>

# 类型
接受者名称, 统一采用单字母 `p` 而不是 `this`, `me` 或者 `this`:

```go
type T struct {}

func (p *T) Get() {}
```

接受者的类型如果不清楚, 统一采用指针型:

```go
func (p *T) Get() {}
```

而不是:

```go
func (p T) Get() {}
```

在某些情况下, 出于性能的考虑, 或者类型本来就是引用类型, 存在以下的特例情况:

- __如果接收者是 `map`、`slice`、`chan`, 不要用指针传递:__

```go
// Map
package main

import (
    "fmt"
)

type mp map[string]string

func (m mp) Set(k, v string) {
    m[k] = v
}

func main() {
    m := make(mp)
    m.Set("k", "v")
    fmt.Println(m)
}
```

```go
// Channel
package main

import (
    "fmt"
)

type ch chan interface{}

func (c ch) Push(i interface{}) {
    c <- i
}

func (c ch) Pop() interface{} {
    return <-c
}

func main() {
    c := make(ch, 1)
    c.Push("i")
    fmt.Println(c.Pop())
}
```

- __如果需要对 `slice` 进行修改, 通过返回值的方式重新赋值:__

```go
// slice
package main

import (
    "fmt"
)

type slice []byte

func (s slice) addOne(b byte) []byte {
    return append(s, b)
}

func main() {
    s := make(slice, 0)
    s = s.addOne(42)
    fmt.Println(s)
}
```

- __如果接收者是含有 `sync.Mutex` 或者类似同步字段的结构体, 必须使用指针传递, 以避免复制:__

```go
package main

import (
    "sync"
)

type T struct {
    m sync.Mutex
}

func (t *T) lock() {
    t.m.Lock()
}

/*
Wrong !!!
func (t T) lock() {
    t.m.Lock()
}
*/

func main() {
    t := new(T)
    t.lock()
}
```

- __如果接收者是大的结构体或者数组, 使用指针传递会更有效率:__

```go
package main

import (
    "fmt"
)

type T struct {
    data [1024]byte
}

func (t *T) Get() byte {
    return t.data[0]
}

func main() {
    t := new(T)
    fmt.Println(t.Get())
}
```
