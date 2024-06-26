---
layout: post
title: Golang 6 - Concurrency - Goroutines
tags: [golang, concurrency, Goroutines]
---
Trong bài trước, chúng ta đã thảo luận về concurency và sự khác biệt giữa concurency và parallelism. Trong bài này chúng ta sẽ nói về cách Go handle concurrency sử dụng `Goroutines`


## Goroutines là gì?
Goroutinues là function hay method chạy concurrenly với function hay methods khác. Goroutinues giống như một lightweight threads. The cost của việc tạo Goroutinnes rất nhỏ so với thread. Một chương trình Go thường có hàng ngànn Goroutines chạy concurrenly. 


## Goroutines vs threads
- Goroutines chỉ chiếm vài KB và có thể tăng hoặc giảm tuỳ theo nhu cầu của ứng dụng. Trong khi thread phải cố định kích thước khi khởi tạo.
- Goroutines giao tiếp dựa vào channels. Channel trong Go được thiết kế để tránh hiện tưởng race conditions khi truy cập vào vùng nhớ chung khi sử dụng Goroutines. Channels giông như ống để giao tiếp giữa các Goroutines

## Làm thế nào để bắt đầu với Goroutines?

Goroutines chạy concurrently bằng việc bắt đầu một method hay function với từ khoá `go`.

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

Ở hàm main, `go hello()` start một Goroutine, hàm gọi `hello()` sẽ chạy concurrently với hàm `main()`. Hàm main có default một Goroutine và được gọi là main Goroutine.

Kết quả
```console
main function

Program exited.
```

Ngạc nhiên chưa!!!!

Chỉ có đoạn text `main function` hiển thị, đoạn text `hello world goroutine` không xuất hiện. Có 2 ý chính cần phải hiểu để giải thích vì sao điều đó xảy ra:
- Đầu tiên, khi một Goroutinne được start, nó được trả về ngay lập tức. Không giống như gọi hàm thông thường, call a Goroutine funnction sẽ được return ngay lập tức để chạy dòng lệnh tiếp theo. Mọi giá trị trả về từ Goroutine sẽ bị ignored.
- Nếu main Goroutine kết thúc, không có Goroutine nào khác chạy.

Bây giờ chúng ta đã hiểu vì sao đoạn text `Hello world goroutine` không được in. Sau khi call `go hello()`, the control ngay lập tức chạy dòng code tiếp theo, in ra `main function` và kết thúc chương trình và `hello` Goroutines không có cơ hội nào được chạy.

Sửa đoạn code trên như sau
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

Kết quả

```console
Hello world goroutine
main function

Program exited.
```

Lần này hàm `main` sleep 1 giây, call `go hello()` có đủ thời gian để execute trước khi main Goroutine kết thúc. Chương trình in ra `Hello world goroutine` sau đó đợi 1 giấy và in tiếp `main function`

Ghi chú: Sử dụng hàm `Sleep()` chỉ là một hack way để hiểu cách Goroutine hoạt động. `Channel` có thể được dùng để giao tiếp với main Goroutine để block main Goroutine cho tới khi hello Goroutine kết thúc. Chúng ta sẽ thảo luận vấn đề này sau.

## Start multiple Goroutines

Thử một đoạn code sau với nhiều Goroutine hơn

```go
package main

import (  
    "fmt"
    "time"
)

func numbers() {  
    for i := 1; i <= 5; i++ {
        time.Sleep(250 * time.Millisecond)
        fmt.Printf("%d ", i)
    }
}
func alphabets() {  
    for i := 'a'; i <= 'e'; i++ {
        time.Sleep(400 * time.Millisecond)
        fmt.Printf("%c ", i)
    }
}
func main() {  
    go numbers()
    go alphabets()
    time.Sleep(3000 * time.Millisecond)
    fmt.Println("main terminated")
}

```
Trong đoạn code trên, có 2 Goroutine được start, `number` and `alphabets` Goroutine. The `number` Goroutine, ngủ 250ms sau đó in ra giá trị của biến `i`, sau đó ngủ tiếp và in tiếp .... cho tới khi i = 5. Tương tư cho `alphabets` Goroutine. 

Output của chươnng trình
```shell
1 a 2 3 b 4 c 5 d e main terminated
```
Hình minh hoạ chương trình trên: 

![Giải thích Goroutine](https://golangbot.com/content/images/2017/07/Goroutines-explained.png)


Bài viết được lược dịch từ: [https://golangbot.com/goroutines/](https://golangbot.com/goroutines/)


