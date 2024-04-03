---
layout: post
title: A Quick Guide for concurrency in Go
tags: [golang, concurrency]
---

## Introduction to Concurrency 
Concurrency: ability `to deal` with many task at once. It quite similar with `parallelism` but let consider 2 examples to show the different of them.

Example 1: Imagine you're running in a stadium, and suddenly, one of your shoelaces comes undone. You stop running, take a moment to tie your shoelace, and then resume running. This ability to handle both running and tying your shoelace demonstrates concurrency. You can multitask and deal with multiple things at once, such as running and performing a quick task like tying your shoelace.

Example 2: Now, picture yourself still running in the stadium, but this time, you're also listening to music on your iPhone. In this scenario, you're able to engage in parallelism. While running, you can simultaneously enjoy your favorite tunes, demonstrating the capability to carry out multiple activities at the same time. This parallelism allows you to do multiple tasks, like running and listening to music on your iPhone.

Let's see the image below to understand the different between `concurrency` and `parallelism`

![alt text](https://golangbot.com/content/images/2017/06/concurrency-parallelism-copy.png)

Concurrency support in Go by `goroutinnes` and `channels`. Let's go detail of them.

## Goroutines
Goroutines are part of making concurrency easy to use.
Goroutines are functions or methods that run `concurrently` with other functions or methods. It very tiny, few KB in stack size, and one Go program can has thousands of Goroutines. 

<a href="https://go.dev/doc/faq#goroutines">Why goroutines instead of threads?</a>

example of how Goroutine in Go

```go
  package main

  import (
  	"fmt"
  )
  
  func hello() {
  	fmt.Println("Hello world goroutine")
  }
  func main() {
  	go hello()
  	fmt.Println("main function")
  }
```
<a href="https://go.dev/play/p/zC78_fc1Hn" target="_black">Run it</a>

we define a function named `hello` and call it in `main()` function with the keyword `go` before. The output:

```console
main function

Program exited.
```
Surprise!!!! why it doesn't display `"Hello world goroutine`????

Some key points here:
 - When a new Goroutine is started, the goroutine call returns immediately. Unlike functions, the control does not wait for the Goroutine to finish executing. The control returns immediately to the next line of code after the Goroutine call and any return values from the Goroutine are ignored.
- The main Goroutine should be running for any other Goroutines to run. If the main Goroutine terminates then the program will be terminated and no other Goroutine will run.

Fix it:

```go
package main

import (  
    "fmt"
    "time"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    time.Sleep(1 * time.Second)
    fmt.Println("main function")
}
```
We add `time.Sleep(1 * time.Second)` to keep main goroutine wait hello goroutine.

## Channels

Channels can be thought of as pipes using which Goroutines communicate. 

Declare `chan T` is a channel of type T, only type T can thought in channel.

Because channel is a piple, we can send and receive data in channel.

```go
data := <- a // read from channel a
a <- data // write to channel a
```

Sends and receives to a channel are blocking by default. When data is sent to a channel, the control is blocked in the send statement until some other Goroutine reads from that channel. Similarly, when data is read from a channel, the read is blocked until some Goroutine writes data to that channel.

Remenber, last program we need to use `time.Sleep` to make main goroutines wait hello goroutine, let's review it use `channel`

```go
package main

import (
	"fmt"
)

func hello(done chan bool) {
	fmt.Println("Hello world goroutine")
	done <- true
}
func main() {
	done := make(chan bool)
	go hello(done)
	<-done
	fmt.Println("main function")
}
```
Link: https://go.dev/play/p/I8goKv6ZMF

More complex example

```go
package main

import (  
    "fmt"
)

func calcSquares(number int, squareop chan int) {  
    sum := 0
    for number != 0 {
        digit := number % 10
        sum += digit * digit
        number /= 10
    }
    squareop <- sum
}

func calcCubes(number int, cubeop chan int) {  
    sum := 0 
    for number != 0 {
        digit := number % 10
        sum += digit * digit * digit
        number /= 10
    }
    cubeop <- sum
} 

func main() {  
    number := 589
    sqrch := make(chan int)
    cubech := make(chan int)
    go calcSquares(number, sqrch)
    go calcCubes(number, cubech)
    squares, cubes := <-sqrch, <-cubech
    fmt.Println("Final output", squares + cubes)
}
```
Link: https://go.dev/play/p/4RKr7_YO_B

## Other features in Go
- Buffered channel
- Select and mutex

## Conclusion

## Resource:
- https://go.dev/tour/welcome/1
- https://golangbot.com/concurrency/

