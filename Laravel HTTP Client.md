# laravel HTTP Client

> laravel 透過 Guzzle 這個套件，模擬 http client 來發送 request
> 預設情況下，laravel 已經自動包含了這個套件。
> 若尚未安裝，可透過 composer 再安裝一次。
>
> ```bash
> composer require guzzlehttp/guzzle
> ```

## 建立 request

建立基礎的 GET request

```php
use Illuminate\Support\Facades\Http;

$response = Http::get('http://example.com');
```

可以直接將查詢字串(query string)加到 URL 上，或是傳入一組索引鍵/值配對的陣列作為 get 的第二個引數

```php
$response = Http::get('http://example.com/users', [
    'name' => 'Taylor',
    'page' => 1,
]);
```

get 方法會回傳 Illuminate\Http\Client\Response 的實體，該實體提供了許多用來取得 Response 資訊的方法：

```php
$response->body() : string;
$response->json($key = null) : array|mixed;
$response->object() : object;
$response->collect($key = null) : Illuminate\Support\Collection;
$response->status() : int;
$response->ok() : bool;
$response->successful() : bool;
$response->redirect(): bool;
$response->failed() : bool;
$response->serverError() : bool;
$response->clientError() : bool;
$response->header($header) : string;
$response->headers() : array;
```

POST PUT PATCH 等 HTTP action 傳送額外資料，一般情況下，資料會使用 json 的資料型別來傳遞

```php
use Illuminate\Support\Facades\Http;

$response = Http::post('http://example.com/users', [
    'name' => 'Steve',
    'role' => 'Network Administrator',
]);
```

若想用 x-www-form-urlencoded 來傳送資料，要在建立 request 前呼叫 asForm() 方法

```php
$response = Http::asForm()->post('http://example.com/users', [
    'name' => 'Sara',
    'role' => 'Privacy Consultant',
]);
```

## 錯誤處理

laravel HTTP client 不會在 client 端或是 server 端錯誤時拋出異常(status code 400 or 500)。

此時可以透過 successful, clientError 或是 serverError 方法來判斷是否發生錯誤。

```php
// 若 status code 在200 ~ 300
$response->successful();

// 若 status code 大於 400
$response->failed();

// 若 status code 為400層級的錯誤
$response->clientError();

// 若 status code 為500層級的錯誤
$response->serverError();
```
