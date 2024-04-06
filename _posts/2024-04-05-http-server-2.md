---
layout: post
title: Golang HTTP Server 2 - Kết nối server với database
tags: [golang, gin, http server]
---

Trong bài trước chúng ta đã dựng được một server đơn giản bằng Gin, trong bài này chúng ta sẽ đóng gói chương trình bằng Docker, tạo Postgress SQL và thử kết nối tới server.

## Dockerfile

Tạo một file tại root folder, với tên `Dockerfile`


```docker
FROM golang:alpine

RUN mkdir /app

WORKDIR /app

ADD go.mod .
ADD go.sum .

RUN go mod download
ADD . .

RUN go install github.com/githubnemo/CompileDaemon

EXPOSE 8000

ENTRYPOINT CompileDaemon --build="go build main.go" --command=./main
```

Hãy xem file `Dockerfile` trên

FROM: FROM xác định image cơ sở để sử dụng cho container. Image `golang:1.16` là một image dựa trên Linux đã cài đặt golang và không có bất kỳ chương trình hoặc phần mềm bổ sung nào khác.

WORKDIR: WORKDIR thay đổi thư mục làm việc. Trong trường hợp này, thư mục làm việc được đặt là /app. Nó thiết lập một thư mục làm việc cho các lệnh tiếp theo.

ADD: Lệnh ADD sao chép tệp từ một vị trí này sang vị trí khác. ADD [NGUỒN] [ĐÍCH] là cú pháp của lệnh. Tương tự, có một lệnh COPY cũng có mục đích tương tự. Ở đây, chúng tôi sao chép tệp go.sum và go.mod trước để có tất cả các thư viện được cài đặt trước khi sao chép tất cả các tệp khác.

RUN: Lệnh RUN thực thi các lệnh trong một lớp mới trên image hiện tại và lưu kết quả. image kết quả được lưu sẽ được sử dụng cho bước tiếp theo trong Dockerfile.

EXPOSE: EXPOSE chỉ định rằng các dịch vụ chạy trên container Docker sẽ được sử dụng trên cổng 8000.

ENTRYPOINT: Điểm nhập (ENTRYPOINT) chạy lệnh bên trong container sau khi tạo container từ một image. Bạn chỉ có thể có một lệnh ENTRYPOINT trong Dockerfile. Nếu sử dụng nhiều lệnh ENTRYPOINT, chỉ lệnh cuối cùng sẽ được thực thi. Ở đây, sau khi tạo container, lệnh ENTRYPOINT sẽ chạy dự án golang của chúng tôi.

Tạo tiếp file Docker compose: `docker-compose.yml`

```docker
version: '3.8'
services:
  db:
    image: postgres:14.1-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - ".:/app"
    depends_on:
      - db
    links:
      - "db:database"
volumes:
  db:
    driver: local
```

Trong file `docker-compose.yml` phía trên, chúng ta khai báo 2 dịch vụ, Postgres và web, posgre được tải về từ image `postgres:14.1-alpine`. Trong thử mục `gin-web-server` run commandn sau `docker compose up --build`. Image không có trong máy sẽ dược tải từ Dockerhub.

Test lại với trình duyệt hoặc Curl
```console
 curl http://localhost:8000
{"data":"Hello World !"}
```

## Setting Up The Database

Chúng ta sẽ sử dụng thư viện GORM, đó là một thư viện tuyệt vời cho ORM trong Go. Tìm hiểu thêm thư viện này tại [ https://gorm.io/docs/index.html ]( https://gorm.io/docs/index.html)

### Cài đặt GORM và Postgress driver

Gõ `Ctrl+C` để thoát chương trình docker đang chạy, sau đó install GORM và PostgresSQL driver

```console
go install -u gorm.io/gorm
go get -u gorm.io/driver/postgres
```

Tạo file `infrastructure/db.go` và copy đoạn code sau để connect tới db

```go
package infrastructure

import (
	"fmt"
	"log"
	"os"

	"github.com/joho/godotenv"
	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

// Database struct
type Database struct {
	DB *gorm.DB
}

// LoadEnv loads environment variables from .env file
func LoadEnv() {
	err := godotenv.Load(".env")
	if err != nil {
		log.Fatalf("unable to load .env file")
	}
}

// NewDatabase : intializes and returns mysql db
func NewDatabase() Database {
	USER := os.Getenv("POSTGRES_USER")
	PASS := os.Getenv("POSTGRES_PASSWORD")
	HOST := os.Getenv("POSTGRES_HOST")
	PORT := os.Getenv("POSTGRES_PORT")
	DBNAME := os.Getenv("DB_NAME")

	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s sslmode=disable TimeZone=Asia/Shanghai", HOST, USER, PASS, DBNAME, PORT)
	fmt.Println("dsn: ", dsn)

	db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})

	if err != nil {
		panic("Failed to connect to database!")

	}
	fmt.Println("Database connection established")
	return Database{
		DB: db,
	}

}

```

Đoạn code trên khởi tạo một connect tới Postgres bằng thông tin đọc từ file `.env`. Chương trình sử dụng thư viện `godotenv` để đọc biến env. Cần cài đặt thư viện này bằng câu lệnh

```console
go get github.com/joho/godotenv
```

Đồng thời, chúng ta phải cập nhật một chút file `docker-compose.yml` để thêm file SQL cho việc khởi tạo database

```
version: '3.8'
services:
  db:
    như cũ
    volumes: 
      - db:/var/lib/postgresql/data
      - ./infrastructure/data.sql:/docker-entrypoint-initdb.d/init.sql <== thêm dòng này
  như cũ
```

Cập nhật file `main.go` để load env, connect DB, và tạo Gin server

```go
package main

import (
	"net/http"
	"todolist/infrastructure"

	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default() //new gin router initialization
	router.GET("/", func(context *gin.Context) {
		infrastructure.LoadEnv()     //loading env
		infrastructure.NewDatabase() //new database connection
		context.JSON(http.StatusOK, gin.H{"data": "Hello World !"})
	}) // first endpoint returns Hello World
	router.Run(":8000") //running application, Default port is 8080
}
```

Ok!!! Mọi thứ đã xong, thử test lại chương trình bằng cách start lại docker compose 
```console
docker compose up --build
```

Nếu tất cả mọi thử đều ổn, bạn sẽ thấy dòng thông báo kết nối được tới database trên log khi truy cập vào server.

```console
gin-web-server-web-1  | 2024/04/06 01:47:22 stdout: Database connection established
gin-web-server-web-1  | 2024/04/06 01:47:22 stdout: [GIN] 2024/04/06 - 01:47:22 | 200 |   29.178479ms |    192.168.65.1 | GET      "/"
gin-web-server-web-1  | 2024/04/06 01:47:23 stdout: [GIN] 2024/04/06 - 01:47:23 | 404 |         613ns |    192.168.65.1 | GET      "/favicon.ico"
```


