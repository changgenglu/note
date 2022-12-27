# [Laravel Middleware](https://learnku.com/docs/laravel/8.x/middleware/9366)

## 定義 middleware

```bash
php artisan make:middleware CheckAge
```

在 `app/Http/Middleware` 目錄之下產生一個新的 CheckAge class，並在其中建立規則：僅允許 `age` 參數大於 200 的請求路徑進行訪問，否則將重新導向至 `home` 頁面

```php
namespace App\Http\Middleware;

use Closure;

class CheckAge
{
    /**
     * 处理传入的请求
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('home');
        }

        return $next($request);
    }
}
```

可以設定 middleware 的動作是在 request 之前或是之後

```php
// 在request 之前
class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // Perform action

        return $next($request);
    }
}

// 在request 之後
class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        // Perform action

        return $response;
    }
}
```

## 註冊 Middleware
