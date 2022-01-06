---
title: Effective Go阅读笔记
date: '2022-01-05'
tags: ['Golang', '笔记']
draft: false
summary: Effective Go阅读笔记
---

本篇记录一下自己在阅读 Effective Go 时的阅读笔记，因为主要目的是边读边记、增强自己的印象，所以可读性应该不佳，如果想要好好学习《Effective Go》，还是建议读[原文](https://go.dev/doc/effective_go)。Effective Go 这份文档写的非常精炼，花几个小时时间不仅可以学到一些语法规范，还可以学到 Golang 的一些设计思想。

1. Formatting
   a. 包括 indenting 和 alignment，在 go 中都由 gofmt 这个程序自动做，不需要人来进行
2. 注释
   a. godoc 可以自动为 package 生成注释，并提供一个 web server
   b. 每个 package 的开头应该包含描述该 package 功能的注释，对于 multi-file 的 package，只要在一个 file 中包含即可
   c. 尽管可以把所有的全局变量声明放在一个 var()中，但是不推荐这样做，因为这样文档不友好，好的方法是：把相关的变量声明放在一个 var()中，然后在声明之前写注释，godoc 会为相应的一组声明统一生成一条文档
3. name
   1. go 推荐 Package name 是单一的单词，而不是下划线或者驼峰命名的复杂单词。尽管可能有两个包名相同的情况，但是此时可以给 import 进来的 package 重命名
   2. 如果使用 import . "xxxx"，那么被 import 的包的方法可以直接用方法名调用，而不是用包名.方法名()来调用，但是这种方法不被推荐，因为假设包名为 ring，方法名为 New，那么 ring.New()很明显表示新建一个 Ring 结构体，但是用了 import .之后，就可以只用 New()了，这会导致代码不易读。
   3. go 中不推荐 Get 开头的函数，比如 work.GetMoney()，go 认为使用 work.Money()就够了
   4. interface 的命名，如果是单一函数接口，可以直接使用函数名+er 作为接口名。如果你自己实现了一个与常用函数功能相同的函数（比如 String()函数），请与常用函数用相同的名称
   5. go 推荐使用驼峰命名而非下划线命名
4. 持续阅读中，未完待续...
