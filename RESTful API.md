# RESTful API

> RESTful API 是一種設計模式
>
> 定義一組"物件"(object)，他們是可以被操作的。
> 物件運用一組固定"動作"(action)簡稱(CRUD)
>
> - 創建(create)
> - 刪除(delete)
> - 更新(update)
> - 讀取(read)
>
> 主流以 JSON 格式做資料傳遞
> 基本上會包含 URL\Object\Action

## 常用 HTTP 動詞

- GET: 讀取資源(不會變動或是更改到伺服器的資訊，主要用來查資料)
- POST: 新增資料
- DELETE: 刪除資料
- PUT: 替換資源
- PATCH: 更新資源

如以 Post(文章)這個物件舉例

| HTTP 動詞 |      URL       |           功能           |                                       說明                                       |
| :-------: | :------------: | :----------------------: | :------------------------------------------------------------------------------: |
|   POST    |  api/v1/posts  |       發表一篇文章       |        如果有相同的請求送第二次，會回傳新的一筆資料，內容一樣只有 ID 不同        |
|  DELETE   | api/v1/posts/1 |     刪除 id=1 的文章     |                 如果發送兩次請求，第二次回傳找不到資源的錯誤訊息                 |
|    PUT    | api/v1/posts/1 |    id=1 資料整筆替換     |                  替換整筆資料，有點像舊資料的刪除，寫入新的資料                  |
|   PATCH   | api/v1/posts/1 | 更新文件 id=1 的部分內容 | 只替代掉部分內容，內容會依照發送請求的資料作修改。如果沒有填寫的部分保留原始資料 |

- 其他不符合以上類別的動作用 POST

## Laravel RESTful API

### 建立 controller

```bash
php artisan make:controller Api/TestController --api
```

```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class TestController extends Controller
{
    public function index()
    {
        // 列出所有
    }

    public function store(Request $request)
    {
        // 建立資料
    }

    public function show($id)
    {
        // 顯示指定 id 的資料
    }

    public function update(Request $request, $id)
    {
        // 更新指定 id 的資料
    }

    public function destroy($id)
    {
        // 刪除指定 id 的資料
    }
}
```

### 加入 api

routes/api.php

```php
Route::middleware('auth:api')->group(function () {
    Route::apiResource('test', 'TestController');
});
```

### URL

| action |                 url                  | Controller function |
| :----: | :----------------------------------: | :-----------------: |
|  get   |     `{{ server-url }}/api/test`      |        index        |
|  post  |     `{{ server-url }}/api/test`      |        store        |
|  get   | `{{ server-url }}/api/test/{{ id }}` |        show         |
| patch  | `{{ server-url }}/api/test/{{ id }}` |       update        |
| delete | `{{ server-url }}/api/test/{{ id }}` |       destroy       |
