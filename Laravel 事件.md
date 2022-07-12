# Laravel 事件

> Event 類別一般儲存在 app/Event 目錄下
> Listener 類別則存在 app/Listeners 目錄下

## 註冊 Event 與 Listener

首先在 laravel 專案中的 App\Providers\EventServiceProvider 註冊事件和監聽者

- `$listen` 屬性是一個陣列，包含所有 `Event`(key) 和其 `listener`(value)
- 可以使用 `php artisan event:list` 來列出所有註冊的 `Event` 和 `listener`

```php
use App\Events\OrderShipped;
use App\Listeners\SendShipmentNotification;

/**
 * The event listener mappings for the application.
 *
 * @var array
 */
protected $listen = [
    OrderShipped::class => [
        SendShipmentNotification::class,
    ],
];
```

### 產生 Event Listener

在 EventServiceProvider 中註冊後，使用 artisan 指令，即可產生 EventServiceProvider 中已註冊但尚未生成的 Event 和 Listener

```bash
php artisan event:gnerate
```

或者也可以分別建立 Event 和 Listener

```bash
php artisan make:event PodcastProcessed

php artisan make:listener SendPodcastNotification --event=PodcastProcessed
```

### 手動註冊

除了在 EventServiceProvider 的 class 中宣告 $listen 屬性外，也可以在 class 中的 boot() 方法手動註冊基於 class 或是匿名函數的 Listener

```php
use App\Events\PodcastProcessed;
use App\Listeners\SendPodcastNotification;
use Illuminate\Support\Facades\Event;

/**
 * Register any other events for your application.
 *
 * @return void
 */
public function boot()
{
    Event::listen(
        PodcastProcessed::class,
        [SendPodcastNotification::class, 'handle']
    );

    Event::listen(function (PodcastProcessed $event) {
        //
    });
}
```

#### 一個 Listener 處理多個 Event

使用 `*` 作為萬用字元參數來註冊 Listener，實現一個監聽者對應多個事件

```php
Event::listen('event.*', function ($eventName, array $data) {
    //
});
```

- 此監聽者接收事件名作為第一個參數，並將整個事件數據，作為第二個參數

### Event Discovery 事件發現

啟用事件發現時，laravel 會搜尋專案的 `app/Listener` 目錄自動尋找並註冊事件與監聽器。  
此外，列在 `EventServiceProvider` 中有被正確定義的事件也會被註冊。

在預設中，事件發現預設是關閉，可以在 `EventServiceProvider` 上複寫 `shouldDiscoverEvents()` 方法來啟用

```php
/**
 * Determine if events and listeners should be automatically discovered.
 *
 * @return bool
 */
public function shouldDiscoverEvents()
{
    return true;
}
```

## 定義 Event

Event class 基本上就是一個資料容器，用來保存與該事件相關的資訊。

假設有一個事件會接收 Eloquent ORM 的物件，`App\Events\OrderShipped`

```php
<?php

namespace App\Events;

use App\Models\Order;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class OrderShipped
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    /**
     * The order instance.
     *
     * @var \App\Models\Order
     */
    public $order;

    /**
     * Create a new event instance.
     *
     * @param  \App\Models\Order  $order
     * @return void
     */
    public function __construct(Order $order)
    {
        $this->order = $order;
    }
}
```

- 在 Event class 中不包含邏輯，只作為已付款訂單 `App\Models\Order` 實體的容器

## 定義 Listener

事件監聽器會在 handle() 中接收 Event 實體，當使用 artisan 指定建立監聽器時，會自動載入相對應的 Event class，並在 handle() 做 Event 的型別提示

```php
<?php

namespace App\Listeners;

use App\Events\OrderShipped;

class SendShipmentNotification
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     *
     * @param  \App\Events\OrderShipped  $event
     * @return void
     */
    public function handle(OrderShipped $event)
    {
        // Access the order using $event->order...
    }
}
```

### 停止事件的傳播

若要停止將某個事件傳播到另一個監聽器上，只要在監聽器的 handle 方法是回傳 false 即可

## 分派 Event

呼叫事件上的靜態方法 `dispatch` ，此方法由 `Illuminate\Foundation\Events\Dispatchable` 提供

任何傳入此方法的值，會被傳給 Event 的 Constructor

```PHP
<?php
 
namespace App\Http\Controllers;
 
use App\Events\OrderShipped;
use App\Http\Controllers\Controller;
use App\Models\Order;
use Illuminate\Http\Request;
 
class OrderShipmentController extends Controller
{
    /**
     * Ship the given order.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $order = Order::findOrFail($request->order_id);
 
        // Order shipment logic...
 
        OrderShipped::dispatch($order);
    }
}
```

## 實作範例

> [參考資料](https://segmentfault.com/a/1190000010730545)