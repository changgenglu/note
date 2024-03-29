# Laravel 學習筆記

> 請先完成 laravel 環境設置

- [Laravel 學習筆記](#laravel-學習筆記)
  - [基礎建立](#基礎建立)
  - [連線資料庫將資料顯示在畫面上](#連線資料庫將資料顯示在畫面上)
    - [mysql](#mysql)
    - [redis](#redis)
  - [新增一個 html 測試`input`到資料庫](#新增一個-html-測試input到資料庫)
    - [將變數傳入 `view` 的三種方法](#將變數傳入-view-的三種方法)
  - [Controller](#controller)
    - [生成 controller](#生成-controller)
    - [resource controller function](#resource-controller-function)
    - [controller 傳入參數](#controller-傳入參數)
    - [參數預設值](#參數預設值)
  - [Route](#route)
    - [route 基本寫法](#route-基本寫法)
    - [resource controller 資源控制器](#resource-controller-資源控制器)
    - [route 分組](#route-分組)
  - [Laravel 功能實現](#laravel-功能實現)
    - [儲存檔案並刪除舊檔](#儲存檔案並刪除舊檔)
  - [Class not found](#class-not-found)
  - [框架設計模式](#框架設計模式)
    - [每一層的職責](#每一層的職責)
    - [MVC 框架](#mvc-框架)
    - [Web API Service](#web-api-service)

## 基礎建立

- 建立新的專案

  ```cmd
  laravel new ProjectName
  ```

- 安裝指定本版

  ```cmd
  composer create-project laravel/laravel=6.* ProjectName
  ```

- 同時建立 migration controller model

  ```cmd
  php artisan make:model New -mcr
  ```

- 建立 Controller

  - 控制器路徑 app/Http/controllers/NewController.php
  - 控制器名稱字首需大寫

  ```cmd
  php artisan make:controller NewController
  ```

- 啟動 Laravel 伺服器

  ```cmd
  php artisan serve
  ```

- 使用路由

  ```php
  // routes/web.php
  Route::get('/home/news', "App\Http\Controllers\NewController@index");

  // app/Http/controllers/NewController.php
  public function index()
  {
    return "<h1>OK</h1>";
  }
  ```

## 連線資料庫將資料顯示在畫面上

### mysql

- Laravel 資料庫設定檔 `.env`

  ```php
  APP_NAME=Laravel        （專案的名稱）
  APP_ENV=local           （專案開發的環境，local / staging）
  APP_KEY=                (APP KEY)
  APP_DEBUG=true          （提供在瀏覽器中顯示詳細的錯誤訊息來進行debug）
  APP_URL=http://localhost（專案網址，EX. http://example.com，使用方法url()時便可取得該網址）

  LOG_CHANNEL=stack

  DB_CONNECTION=mysql (使用的資料庫)
  DB_HOST=127.0.0.1   (資料庫主機位置)
  DB_PORT=3306        (資料庫的埠號)
  DB_DATABASE=test    (資料庫名稱)
  DB_USERNAME=        （資料庫帳號）
  DB_PASSWORD=        （資料庫密碼）
  ```

- 建立一個 model

  - model 路徑 app/Models/News.php

  ```cmd
  php artisan make:model News
  ```

- Controller 參用 News model

  ```php
  use App\Models\News;
  ```

- 如何把陣列顯示在前台 (回傳`json`格式)

  ```php
  public function index()
  {
    $dataList = News::all();

    return json_encode($dataList);
  }
  ```

- 接收 `Route::post` 的路由接引到 `store(`) 完成資料庫的新增

  ```php
  Route::post('/home/news', "App\Http\Controllers\NewController@store");
  ```

### redis

> 需先在環境安裝 redis

- 安裝 `predis`

  ```bash
  composer require predis/predis
  ```

- redis 預設有 16 個資料庫，Laravel 會使用預設的資料庫`0`
- 修改 `env`

  ```config
  REDIS_HOST=127.0.0.1
  REDIS_PASSWORD=null
  REDIS_PORT=6379
  REDIS_CLIENT=predis
  REDIS_PREFIX=""
  ```

- 使用方法

  ```php
  use Illuminate\Support\Facades\Redis;
  Redis::set('name', 'Vic');
  Redis::get('name');
  ```

- redis-cil

  ```bash
  $ redis-cli
  $ select 0   //選擇資料庫0
  $ keys *     //列出所有keys
  $ get laravel_database_name  //取得key value
  ```

- 須注意 laravel 預設的 redis key 會有 `laravel_database_` 這個前綴：`$ get laravel_database_${your_key}`。這個前綴設定可以在 `env` 中的 `REDIS_PREFIX` 修改

## 新增一個 html 測試`input`到資料庫

- 修改 controller

  ```php
  store(Request $request){
    $newItem = new News();
    $newItem->title = $request->input("title");
    $newItem->title = $request->input("ymd");
    $newItem->save();

    return "進來了";
  }
  ```

- 修改 `VerifyCsrfToken.php`，先略過資料傳送的資安問題

  - 路徑 `/home` 底下都先忽略

  ```php
  protected $except = [
    "/home/*"
  ];
  ```

- 在 model 增加

  ```php
  public $timestamps = false;
  // redirect => 重新導向
  ```

### 將變數傳入 `view` 的三種方法

1. with: 用於簡單傳遞變數，但不易擴充傳遞變數，所以不常用到

   ```php
   $name = "test";
   $age = 23; 

   return view('my_laravel')->with('name', $name);
   // &
   return view('my_laravel')->with('name', $name)->with('age', $age);

   // 用陣列包起來
   $data = [
     'name' = 'test',
     'age'  =26
   ];

   return view('my_laravel')->with('data', $data);

   // view
   {{ $data['name'] }}
   ```

2. Array

   ```php
   $data = [
     'name' => 'test',
     'age' => 26
   ]

   return view('my_laravel', $data)

   // view
   {{ $name }}
   ```

3. compact

   ```php
   // 常用於複雜變數，不用包裝成新的變數名稱
   $data = [
     'name' => 'test',
     'age' => 26
   ];
   $title = 'title';

   return view('my_laravel', compact('data', 'title'));

   // view
   {{ $data['name'] }}  // 因為在 data 陣列中 
   {{ $title }}  // 變數值直接使用
   ```

## Controller

### 生成 controller

```bash
php artisan make:controller NewController
```

- `--resource`

  ```bash
  php artisan make:controller function/NewController --resource
  ```

  - 在`function/` 的目錄下，新增一個資源控制器
  - 生成`index()` `create()` `store()` `show()` `edit()` `update()` `destroy()`

- `--api`

  ```bash
  php artisan make:controller api/NewController --api
  ```

  - 一般 api 控制器會新增在 Controller/api 的目錄之下
  - 生成`index()` `store()` `show()` `update()` `destroy()`，省略 `create()` `edit()` 方法

### resource controller function

- `index()`: 顯示所有資料的列表

- `create()`: 顯示新增畫面
- `store()`: 新增資料
- `show()`: 顯示指定 id 的資料
- `edit()`: 顯示編輯的畫面
- `update()`: 更新資料
- `destroy()`: 刪除資料

### controller 傳入參數

一般參數

```php
public function show($id)
{
   return response()->json(New::find($id), 200);
}
```

構造函數注入(Constructor Injection)

```php
public function show(New $new)
{
    return response()->json($new, 200);
}
```

### 參數預設值

當傳入 controller 的參數為空時，參數返回預設值。

```php
// route/api.php:
Route::post('new/{new?}', 'NewController@show');

// NewController.php:
class NewController extends Controller
{
    public function show($new = "nothing news")
    {
        // ...
    }
}
```

## Route

laravel 中 route 有兩種:`routes/web.php` `routes/api.php`，分別為一般頁面和 api

### route 基本寫法

- 一般參數

  ```php
  Route::get(‘new’, ‘api\NewController@index’);
  Route::get(‘new/{id}’, ‘api\NewController@show’);
  Route::post(‘new’, ‘api\NewController@store’);
  Route::put(‘new/{id}’, ‘api\NewController@update’);
  Route::delete(‘new/{id}’, ‘api\NewController@destroy’);
  ```

- 構造函數注入

  ```php
  Route::get(‘new’, ‘api\NewController@index’);
  Route::get(‘new/{new}’, ‘api\NewController@show’);
  Route::post(‘new’, ‘api\NewController@store’);
  Route::put(‘new/{new}’, ‘api\NewController@update’);
  Route::delete(‘new/{new}’, ‘api\NewController@destroy’);
  ```

  - 第一個參數是對應的路徑，後面有`{}`代表傳入的參數
  - 第二個參數是對應的 controller @後面為 controller 內要呼叫的方法

### resource controller 資源控制器

```php
Route::Resource('new', 'NewController');

// api資源控制器
Route::apiResource('new', 'api\NewController');
```

### route 分組

- prefix: 前綴用來設定 URL 開始共同的部分。

```php
Route::prefix("new")->group(function () {
    Route::get('view', 'NewController@show');
    Route::post('create', 'NewController@create');
    Route::put('update', 'NewController@edit');
    // ...
});

```

- namespace: 若要綁定的 controller 不在預設的 app/Http/Controller 裡，而是有更進一步的分類，可以設定 namespace()方便管理。

```php
// app/Http/Controller/New
Route::namespace("new")->group(function () {
    Route::get('new/view/{id}', 'NewController@show');
    Route::post('new/create', 'NewController@create');
    Route::put('new/update', 'NewController@edit');
    // ...
});
```

- middleware: laravel 進入 action 之前會先對 http request 進行檢查

```php
Route::middleware('adminonly')->group(function () {
    Route::get('new/create', 'NewController@create');
    Route::get('new/{id}/delete', 'NewController@delete');
    // ...
});
```

## Laravel 功能實現

### 儲存檔案並刪除舊檔

```php
public function updateProfile(Request $request)
{
  $user = auth()->user();
  // 表單驗證規則
  $validated = $this->validateUserProfile($request->all(), $user->id)->validate();
  if ($request->has('image')) {
    // 取得資料表中原始資料
    $originalData = User::find(auth()->user()->id)->getAttributes();
    if ($originalData['image']) {
      $filename = $originalData['image'];
      $storage = Storage::disk('upload');
      // 如果資料表中有紀錄，那就刪除檔案
      if ($storage->exists($filename)) {
        $storage->delete($filename);
      }
    }
    // 原始$request['image']的值為暫存路徑，現將其改為資料表中的路徑
    if ($request->hasFile('image')) {
      $validated['image'] = $request->file('image')->store('images/users', 'upload');
    }
  }
  $user->update($validated);
}
```

## Class not found

當出現 `Class 'xxx\\xxx\\xxx\\xxx' not found` 時，可能原因為 composer autoload 尚未註冊或是註冊錯誤。

解決方法：

- 方法一

  ```terminal
  composer dump-autoload -o
  ```

- 方法二

  檢查 vendor/composer 下面的 autoload 資料夾中的檔案 autoload_classmap.php 和 autoload_static.php

## 框架設計模式

在小型專案中，典型的 MVC 架構沒什麼問題，但隨著系統越來越複雜，必須再細分更多層，於是衍生出 View - Presenter - Controller - Service - Repository - Model 六層框架設計模式。

### 每一層的職責

- Model 盡可能隱藏操作資料的 know-how，將資料抽象化，作為一個 Object Relational Mapping。
- Repository 藉由操作 Model，幫助 Service 實現各種商務邏輯對應的資料庫操作方法。
- Service 實現商務邏輯，並且讓 Controller 僅需要專注在溝通上。
- Controller 作為 View 與商務邏輯間的溝通橋樑。
- Presenter 負責 "如何處理資料"
- View 負責"要給客戶看到什麼"

### MVC 框架

> 參考資料：
>
> [Laravel 加入 Repository 與 Service](https://vocus.cc/article/5fa7fe49fd8978000125da22)

若將這六個 layer 的職責對應到 MVC 框架中，小專案下的 model 其實就是 Business Model，包含商業邏輯以及和資料庫溝通。而 View 也不會刻意把資料操作邏輯與資料處理方式獨立成一個 Presenter，因此
小型專案的 View 往往混著一些邏輯判斷。

- Model
  - Service
  - Repository
  - Model
- Controller
  - Controller
- View
  - View
  - Presenter

### Web API Service

通 Web API Service 僅僅是將 service 送來的資料變成 JSON format 輸出到 View 上，所以有時 Controller 就涵蓋了 Presenter 的職責，View 純粹只是 JSON, XML 等格式資料。

- Model
  - Service
  - Repository
  - Model
- Controller
  - Presenter
  - Controller
- View
  - View
