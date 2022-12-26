# Laravel 廣播

> 在 laravel 中想要達到 websocket 的效果，由後端主動發訊息給前端，需使用 broadcasting 將 event 廣播給前端。
>
> 常用的有兩種機制可以選擇：
>
> 1. pusher: 有使用限制，且須收費。
> 2. redis + socket.io: 免費無限制。
>
> 一般業界多選擇使用 redis + socket.io

## 流程

1. 使用 Laravel broadcasting(Redis) 廣播 Event 到 Queue(Redis)
2. Laravel Queue Listener 讀取 Event，並使用 Redis 的 sub / pub 機制將 Event 發送給 laravel-echo-server
3. laravel-echo-server 接收到 Event，並透過 socket.io 將 Event 發送給 laravel-echo
4. laravel-echo 解析接收到的 Event

### 廣播事件的設定檔

- config/broadcasting.php

pusher/ably/redis/log 這些是廣播用的 driver，log 在開發期間使用
