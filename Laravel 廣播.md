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

## 安裝與設定 redis

- 需先安裝 redis

  ```bash
    composer require predis/predis
  ```

  > **phpredis 和 predis**
  >
  > phpredis 是 c 寫的 php 擴充功能，predis 是使用純 php 寫的，
  >
  > 在性能上當然是擴充功能比較好，但兩者之間沒有跨級距的差異。
  >
  > laravel 官方推薦使用 predis，因為純 php 寫的，只需要 composer 就可以安裝，符合 laravel 便捷的思想。
  >
  > 參考資料
  >
  > [phpredis 和 predis](https://learnku.com/articles/7259/phpredis-and-predis)

- .env 設定

```env
QUEUE_CONNECTION=redis
BROADCAST_DRIVER=redis
```

## 註冊 BroadcastServerProvider

在使用廣播之前，需先註冊 BroadcastServerProvider。

將 config/app.php 設定檔中 providers 陣列內的 App\Providers\BroadcastServiceProvider 取消註解。

```php
'providers' => [
        /*
         * Application Service Providers...
         */
        // 取消註解
        // App\Providers\BroadcastServiceProvider::class,
    ],

```

## 新增 Event 來廣播

當出貨狀態更新時，要主動通知前端，就必須把這個 Event 廣播出去

- 新增一個出貨狀態更新的 Event

```php
php artisan make:event ShippingStatusUpdated
```

- 修改 app\Events\ShippingStatusUpdate.php 文件

```php

```