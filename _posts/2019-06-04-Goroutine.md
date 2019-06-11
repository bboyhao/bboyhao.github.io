---
layout: 	post
title: 		Goroutine and Concurrency
subtitle: 	Goroutine 学习总结
date: 		2019-06-04
author: 	bboyhao
header-img:
catalog: 	true
tag:
    - Go
---

>为啥Go的mascot是一只gopher。。。

## Overview
This blog is a summary of 2012 Golang Concurrency talk. The slide can be
found at this [link](https://talks.golang.org/2012/concurrency.slide#1)

## What is concurrency?

Concurrency is the composition of independently executing computations. It is
not parallelism.

## Boring example

When you run the following example from the command line, the caller will return
immediately. Termination of main will takes the boring function down with it.

```Go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main(){
    go boring("boring!")
}

func boring(msg string) {
    for i := 0; ; i++ {
        fmt.Println(msg, i)
        time.Sleep(time.Duration(rand.Intn(1e3)) * time.Millisecond)
    }
}
```

## Goroutines

Goroutine is an independently executing function, lauched by a go statement.
```
    go func {
        do something
    }
```

It has its own call stack, which grows and shrinks as required.
It is very cheap as there can be thousands of goroutines in a program. Go uses
M:N model which allows multiple goroutines to run inside one OS thread.
Basically, a goroutine has dynamic stack size and run inside an OS thread.

## Channel

A channel in Go provides a connection between two goroutines, allowing them to
communicate.
```GO
    c := make(chan int)
    // Sending on a channel
    c <- 1
    // Receive from a channel
    i := <- c
```
Example
```Go
func main() {
    c := make(chan string)
    go boring("boring!", c)
    for i := 0; i < 5; i++ {
        fmt.Printf("You say: %q\n", <-c) // Receive expression is just a value.
    }
    fmt.Println("You're boring; I'm leaving.")
}
func boring(msg string, c chan string) {
    for i := 0; ; i++ {
        c <- fmt.Sprintf("%s %d", msg, i) // Expression to be sent can be any suitable value.
        time.Sleep(time.Duration(rand.Intn(1e3)) * time.Millisecond)
    }
}
```

## Synchronization

When the main function executes <- c, it will wait for a value to be sent.
Similarly, when the boring function executes c <- value, it waits for a receiver
to be ready.

A sender and receiver must both be ready to play their part in the
communication. Otherwise we wait until they are.

Example code
The following code allows Joe and Ann to take turns to talk
```Go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	joe := boring("Joe")
	ann := boring("Ann")
	for i := 0; i < 5; i++ {
		fmt.Println(<-joe)
		fmt.Println(<-ann)
	}
	fmt.Println("You're boring; I'm leaving.")
}

func boring(msg string) chan string {
	c := make(chan string)
	go func() {
		for i := 0; ; i += 1 {
			c <- fmt.Sprintln("%s %d", msg, i)
			time.Sleep(time.Duration(rand.Intn(1e3)) * time.Millisecond)
		}
	}()
	return c
}

```

The following code allows whoever is ready to talk
```Go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	c := fanIn(boring("Joe"), boring("Ann"))
	for i := 0; i < 10; i++ {
		fmt.Println(<-c)
	}
	fmt.Println("You're boring; I'm leaving.")
}

func fanIn(input1, input2 chan string) chan string {
	c := make(chan string)
	go func() {
		for {
			c <- <-input1
		}
	}()
	go func() {
		for {
			c <- <-input2
		}
	}()
	return c
}

func boring(msg string) chan string {
	c := make(chan string)
	go func() {
		for i := 0; ; i += 1 {
			c <- fmt.Sprintln("%s %d", msg, i)
			time.Sleep(time.Duration(rand.Intn(1e3)) * time.Millisecond)
		}
	}()
	return c
}
```
