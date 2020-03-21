---
title: 参观矿大预约小程序后台API
date:  2020-03-20 15:28:33 +0800
category: 后端
tags: golang
excerpt: Golang-Gin框架实现预约API
---

# [参观矿大预约小程序后台API](https://github.com/LijinPengT/GoBookSystem)

## Api-Docs(http://127.0.0.1:8080)

1. 用户认证
    + /auth
    + POST
    ```JSON
        {
            "username": "uname",
            "password": "password"
        }
    ```

    + 返回结果
    ```json
        {
            "code": 200,
            "data": {
                "token": "eyJhbG"
            },
            "msg": "ok"
        }
    ```

    + example
    ```json
        POST /auth HTTP/1.1
        Host: 127.0.0.1:8080
        Content-Type: application/json
        {
            "username": "uname",
            "password": "password"
        }
    ```

2. 创建预约
    + /api/v1/books
    + POST

    ```json
        POST /api/v1/books HTTP/1.1
        Host: 127.0.0.1:8080
        Content-Type: application/json
        {
            "book_id": "15848858",
            "name": "dwyanelee",
            "org": "flyingstudio",
            "phone": "4547887244",
            "date": "2020-03-08 15:03:56",
            "last": 7,
            "desc": "nihao ,cumt",
            "content": "so many people,",
            "created_by": "openid",
            "state": 1
        }
    ```

    + book_id (int) (预约id)
    + date (string) (日期)
    + created_by (string)(创建人)
    + state      (int)(只能是0/1，表示是否禁用)
    + token      (调用api必须的token)
    + 返回结果

    ```json
        {
        "code": 200,
        "data": {
                "book_id": 1,
                "content": "so many people,",
                "created_by": "openid",
                "date": "2020-03-08 15:05:35",
                "desc": "nihao ,cumt",
                "last": 7,
                "name": "dwyanelee",
                "org": "flyingstudio",
                "phone": "4547887244",
                "state": 1
                },
        "msg": "ok"
        }
    ```

3. 获取预约列表
    + /api/v1/books

    + GET
    ```json
        http://127.0.0.1:8080/api/v1/books?token="$token"
    ```

    + 返回结果
    ```json
        {
            "code":200,
            "data":{
                "lists":[],
                "total":0
            },
            "msg":"ok"
        }
    ```

4. 获取单个预约(删除单个预约也是传book_id, 方法为DELETE)
    + /api/v1/books/:book_id
    + GET
    ```json
        https://127.0.0.1:8080/api/v1/books/$book_id?token="$token"
    ```

    + 返回结果
    ```json
        {
            "code":200,
            "data":{},
            "msg":"ok"
        }
    ```

6. 编辑预约(开发ing)

## 登录模块