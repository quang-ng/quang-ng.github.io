---
layout: post
title: Golang HTTP Server 1 - Giới thiệu bài toán và cài đặt environment
tags: [golang, gin, http server]
---

Trong bài này chúng ta sẽ cùng nhau dựng một HTTP server đơn giản bằng cách sử dụng một web framework tên là GIN (https://github.com/gin-gonic/gin). 

Yêu cầu: xây dựng một REST API quản lý TODO, có các API như sau:
- Đăng ký / Đăng nhập 
- Thêm/xoá/sửa TODO, list and detail API, của user hiện tại, mọi request đều require authentication

Tech stack:
- Postgress SQL
- Go 1.21.5
- GIN (https://github.com/gin-gonic/gin)
- Docker

## Giới thiệu về GIN 
Gin là một web framework được viết bởi `go`. Github page `https://github.com/gin-gonic/gin` Với hơn 75.1k sao, 
 Những key feature của Gin phải kể tới

- Zero allocation router
- Fast
- Middleware support
- Crash-free
- JSON validation
- Routes grouping
- Error management
- Rendering built-in
- Extendable

## Setup dev env

- Cài đặt Docker, làm theo từng bước trong trang document của Docker (https://docs.docker.com/). Verify bằng cách:

```console
 docker --version
 ```

Trả về version của Docker là coi như bạn đã cài đặt thành công. Output có dạng. 

```bash
Docker version 24.0.6, build ed223bc
```

Chúng ta sẽ sử dụng Docker để đóng gói chương trình, nên Docker là tool duy nhất cần cài đặt trên máy.


## Giới thiệu về API

Chúng ta sẽ build những API cho việc thêm/xoá/sửa, lấy danh sách, lấy chi tiết TODO list của currennt user. Đây là danh sách API trong tutorial này

Những API sau không yêu cầu authentication:
- POST `/login` - creates session
- POST `/registor` - đăng ký neww user


Những API sau đều yêu cầu authentication, nếu không thì sẽ trả về lỗi 403: 
- GET `/todo/` Get list of TODO of current users, returned as JSON.
- GET `/todo/:id` Get detail of a TODO, returned as JSON
- POST `/todo/` create new todo
- PUT `/todo/:id` cập nhật thông tin TODO
- DELETE `/todo/:id` xoá TODO
- DELETE `/logout` - destroys session


## Create project folder

Mở terminal, tạo một folder cho project
```console
mkdir gin-web-server
cd gin-web-server
```
Trong VS code, mở thư mục vừa tạo, tạo một file mới tên là `docker-compose.yml`




