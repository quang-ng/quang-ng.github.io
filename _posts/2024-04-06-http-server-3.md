---
layout: post
title: Golang HTTP Server 3 - Khởi tạo database và thêm layer model, services, controller
tags: [golang, gin, http server]
---

Trong bài trước, chúng ta đã khởi tạo Postgre database và kết nối tới server, trong bài này chúng ta sẽ tạo một table, khởi tạo seed data, thêm vài layers mới cho dự án.

## Thiết kế model và tạo table

Tạo file `model/todo.go` và follow code:

