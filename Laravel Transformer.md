# Laravel API resource

> api 返回資料若以原生格式返回，沒有經過任何處理，可能包含敏感資訊
>
> 利用 transformer 可以將 api 響應的訊息系統化規則，進行統一輸出，方便管理與編輯
>
> 參考資料：
>
> [Laravel API 系列教程（三）：使用 API Resource 来创建自己的 {JSON:API} 格式 API](https://laravelacademy.org/post/9203)
>
> [Laravel 構建 API 伺服器之響應資料處理](https://www.796t.com/content/1545181747.html)
>
> [Laravel 8 中文文檔 API 资源](https://learnku.com/docs/laravel/8.x/eloquent-resources/9410)

- [Laravel API resource](#laravel-api-resource)
  - [spatie/laravel-fractal 套件](#spatielaravel-fractal-套件)
    - [建立 transformer](#建立-transformer)
    - [在 controller 中使用 transformer](#在-controller-中使用-transformer)
  - [API Resource](#api-resource)

## spatie/laravel-fractal 套件

```bash
composer require spatie/laravel-fractal
```

安裝完成後，在 laravel 中註冊

```bash
php artisan vendor:publish --provider="Spatie\Fractal\FractalServiceProvider"
```

### 建立 transformer

在 app/Http 的目錄下建立 Transformers 目錄

```bash
php artisan make:transformer TestTransformer
```

```php
<?php

namespace App\Transformers;

use League\Fractal\TransformerAbstract;

class TeatTransformer extends TransformerAbstract
{
    /**
     * List of resources to automatically include
     *
     * @var array
     */
    protected $defaultIncludes = [
        //
    ];

    /**
     * List of resources possible to include
     *
     * @var array
     */
    protected $availableIncludes = [
        //
    ];

    /**
     * A Fractal transformer.
     *
     * @return array
     */
    public function transform()
    {
        return [
            'id' => $user->id,
            'name' => $user->name,
            'signature' => $user->signature,
            'created_at' => $user->created_at->toDateTimeString()
        ];
    }
}
```

返回的陣列代表真實要響應的 json 資料格式

### 在 controller 中使用 transformer

匯入 transformer 命名空間

```php
class UserController extends Controller
{

    /**
     * 使用者列表介面
     */
    public function index()
    {
      $users = User::all();

      return $this->response->collection($users, new TestTransformer());
    }

}
```

輸出：

```json
{
  "data": [
    {
      "id": 1,
      "name": "張三",
      "signature": "Hello, World",
      "created_at": "2018-11-02 16:21:20"
    },
    {
      "id": 2,
      "name": "李四",
      "signature": "這個人很懶...什麼也沒有留下",
      "created_at": "2018-11-02 16:21:20"
    }
  ],
  "meta": {
    // ...自動生成的元資料，如果你查詢出的資料帶有分頁資料（laravel中的Paginate）
    // 那麼Transformer將會自動幫你把分頁資料加入在此處
  }
}
```

## API Resource

larave 5.5 新增的 API Resource，和 transformer 功能與思路基本上一樣

但由於是 laravel 官方釋出，因此與 Laravel Eloquent model 各種功能結合的更加緊密

```bash
php artisan make:resource ArticleResource
```

生成的檔案位於 app/Http/Resources 目錄底下

```php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\Resource;

class TestResource extends Resource
{
    /** * Transform the resource into an array.
     *
     * @param \Illuminate\Http\Request $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $user->id,
            'name' => $user->name,
            'signature' => $user->signature,
            'created_at' => $user->created_at->toDateTimeString()
        ];
    }
}
```

在 controller 中將 transformer 實例化

```php
public function show(User $user)
{
    return new ArticleResource($user);
}
```

```json
{
  "data": {
        {
          "id": 1,
          "name": "張三",
          "signature": "Hello, World",
          "created_at": "2018-11-02 16:21:20"
        }
    }

}
```

預設數據會被包在 data 的物件裡面，可以透過 `withoutWrapping()` 來將其去除

```php
public function show(User $user)
{
    TestResource::withoutWrapping();
    return new TestResource($user);
}
```
