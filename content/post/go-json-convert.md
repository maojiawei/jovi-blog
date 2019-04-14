---
title: "Go JSON的序列化问题"
date: 2019-04-14T11:13:01+08:00
draft: false
tags: ["GO"]
categories: "microservice"
toc: true
---
&emsp;最近在使用Go语言，但是go语言在json序列化方面有个隐藏玩法，现在记录一下。  
&emsp;GO语言在使用`encoding/json`时，要求所有的属性名称**首字母大写**，不然，转换将无法成功。  
### 错误转换案例
&emsp;例如，以下代码:
```editorconfig
package main

import (
	"encoding/json"
	"fmt"
)

type JsonObject struct {
	name string
	age int
	blog string
	habbits []string
}

func main() {
	o := JsonObject{
		name: "Jovi",
		age: 25,
		blog: "jovi.io",
		habbits:[]string{"tennis","gundam"},
	}
	a , err := json.Marshal(o)
	if(err != nil){
		fmt.Println("json转换异常")
	}
	fmt.Println(string(a))
}
```
运行结果:
```editorconfig
{}
```
### 转换成功案例
&emsp;于是，我们做了如下修改:
```editorconfig
package main

import (
	"encoding/json"
	"fmt"
)

type JsonObject struct {
	Name string
	Age int
	Blog string
	Habbits []string
}

func main() {
	o := JsonObject{
		Name: "Jovi",
		Age: 25,
		Blog: "jovi.io",
		Habbits:[]string{"tennis","gundam"},
	}
	a , err := json.Marshal(o)
	if(err != nil){
		fmt.Println("json转换异常")
	}
	fmt.Println(string(a))
}
```
&emsp;运行结果如下,可以看出json转换成功:
```editorconfig
{"Name":"Jovi","Age":25,"Blog":"jovi.io","Habbits":["tennis","gundam"]}
```
### 转换重命名
&emsp;在某些特定情况下，我们需要对JSON的名称进行转换，或者我们就需要首字母小写的属性名，于是，我们可以如下修改:  
```editorconfig
package main

import (
	"encoding/json"
	"fmt"
)

type JsonObject struct {
	Name string `json:"name"`
	Age int `json:"age"`
	Blog string `json:"blog"`
	Habbits []string `json:"habbits"`
}

func main() {
	o := JsonObject{
		Name: "Jovi",
		Age: 25,
		Blog: "jovi.io",
		Habbits:[]string{"tennis","gundam"},
	}
	a , err := json.Marshal(o)
	if(err != nil){
		fmt.Println("json转换异常")
	}
	fmt.Println(string(a))
}
```
&emsp;运行结果如下:
```editorconfig
{"name":"Jovi","age":25,"blog":"jovi.io","habbits":["tennis","gundam"]}
```