# [Laravel API Resource](https://docs.cornch.dev/zh-tw/laravel/8.x/eloquent-resources)

###### tags: `php` `Laravel`

> 製作 API 時，利用 API Resource 轉換實際要回傳給使用者的 JSON Response
>
> 例如：
>
> 1. 某些屬性只希望特定的使用者看到
> 2. 在 model 的 JSON 呈現上包含特定的關聯

## 產生 Resource

```php
php artisan make:resource UserResource
```

檔案路徑`app/Http/Resources`

###　 Resource Collection

產生一個 Resource 來轉換一組包含 Model 的 Collection

```php
php artisan make:resource User --collection
&&
php artisan make:resource UserCollection
```

### 概念

Resource class 代表的是需要被轉換為 JSON 結構的單一 Model

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }
}
```

每個 Resource class 都有一個 `toArray` 方法，此一方法回傳一組包含屬性的陣列。
當使用 `$this` 時，Resource class 會自動存取代理(Proxy)到底層的 model ，因此可以直接存取 model 的屬性

透過 route 回傳這個 Resoruce

```php
use App\Http\Resources\UserResource;
use App\Models\User;

Route::get('/user/{id}', function ($id) {
    return new UserResource(User::findOrFail($id));
});
```
