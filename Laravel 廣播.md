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
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class ShippingStatusUpdated implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    // 加入 $order 屬性
    public $order;

    public function __construct()
    {
        // 注入屬性
        $this->order = $order;
    }

    public function broadcastOn()
    {
        // return new PrivateChannel('channel-name');
        return new Channel('order');
    }

    public function broadcastAs()
    {
        return 'OrderUpdated';
    }
}
```

- implements ShouldBroadcast 是為了當 Event 被觸發時，把 Event 廣播出去。
- broadcastOn() 用來設定廣播到哪一個頻道，如果我們只希望這個訂單建立者可以查看更新狀態，就要在訂單的私人頻道廣播這個 Event，(即被註解的部分，line: 26)
- 廣播頻道有三種可以選：Channel/PrivateChannel/PresenceChannel
- Channel 代表任何使用者都可以訂閱公共頻道，而 PrivateChannel 和 PresenceChannel 則代表需要授權的私人頻道。
- broadcastAs() 用來自訂廣播名稱。

## 定義頻道授權規則

- 如果是用 PrivateChannel，則需要定義頻道授權規則，如果是公共頻道(Channel)就不用。
- 在 routes/channels.php 這邊可以定義頻道授權規則，如：驗證任何在 order.{orderId} 的私人頻道上嘗試監聽的使用者是否為實際該訂單的建立人

  ```php
  Broadcast::channel('order.{orderId}', function ($user, $orderId) {
      return $user->id  === Order::findOrNew($orderId)->user_id;
  });
  ```

- 若是用 JWT 來進行驗證時，需修改 broadcastsServiceProvider

  - 原為 Broadcast::routes() 改成：

  ```php
  Broadcast::routes(['middleware' => ['auth:api']]);
  ```

## 自訂廣播名稱

Laravel 預設會使用 Event 類別名稱去廣播事件，自訂廣播名稱需自行定義 broadcastAs() 方法

```php
public function broadcastAs()
{
    return 'orderUpdated';
}
```

## Laravel Echo

- 安裝

  ```bash
  npm install --save socket.io-client
  npm install --save laravel-echo
  ```

- 將 resources/js/bootstrap.js 最下方有寫好的範例程式，去掉註解，改成用 socket.io

```javascript
/**
 * Echo exposes an expressive API for subscribing to channels and listening
 * for events that are broadcast by Laravel. Echo and event broadcasting
 * allows your team to easily build robust real-time web applications.
 */

// import Echo from 'laravel-echo';

// window.Pusher = require('pusher-js');

// window.Echo = new Echo({
//     broadcaster: 'pusher',
//     key: process.env.MIX_PUSHER_APP_KEY,
//     cluster: process.env.MIX_PUSHER_APP_CLUSTER,
//     forceTLS: true
// });
import Echo from "laravel-echo";

window.Pusher = require("socket.io-client");

window.Echo = new Echo({
  broadcaster: "socket.io",
  host: windows.location.hostname + ":6001",
});
```

- build code

  ```bash
  npm run dev
  ```

  - 會將 resources/js build 到 public/js (resources/js/app.js => public/js/app.js)
  - 由於 app.js 中有引用 bootstrap.js，所以在頁面中引用 app.jsa 就能使用 echo 了
  - Laravel Echo 會需要存取當前 session 的 CSRF token，需要在 header 中設定

## Laravel Echo Server

- 安裝 Laravel Echo Server

  ```bash
  npm install -g  laravel-echo-server
  ```

- project 根目錄下，初始化 laravel echo server，所有的問題都用預設

  ```bash
  laravel-echo-server init
  ```

- 會再跟目錄產生一個 laravel-echo-server.json，把 devMode 設定為 true
- 啟動 laravel-echo-server

  ```bash
  laravel-echo-server start
  ```
