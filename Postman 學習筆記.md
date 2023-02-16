# [Postman 學習筆記](https://tw.alphacamp.co/blog/postman-api-tutorial-for-beginners)

## 參數區介紹

### Params 網址參數頁

設定 Query Params 搜尋參數 Path Variables 路徑變數

- 預設只有 Query Params 搜尋參數，Path Variable 路徑變數，需要自行在網址上打上冒號＋變數名 (ex. " : name " )，才會出現
- 設定方式都是採 Key - value ，可以根據情況彈性勾選

### Authorization 驗證設定頁

用來設定 Header 中的 Authorization 參數

- No Auth | 不需要驗證
- Basic Auth ｜帳號，密碼型驗證
- token 驗證 ｜ token 型驗證

### Header

用來設定 Header 中的其他參數

postman 把一些必要的參數隱藏起來，如需特殊設定，可以取消隱藏，進行修改。

- User-Agent | 告知 Server，發出 Request 的 Client 瀏覽器、作業系統等資訊
- Accept ｜告知 Server，Client 可以解讀的內容類型
- Content-type | 告知 Server，Client 提交什麼類型內容

### body

較常用的是 form-data, x-www-form-urlencoded, raw，前兩者都是送出表單資料，最後一個提供較多彈性的資料格式。

- form-data 不會針對內容進行編碼，可選擇 file 類型進行上傳檔案
- x-www-form-urlencoded 會以 Key = val1 進行編碼，一般的表單資料使用
- raw 放 postman JSON 資料

## Laravel CSRF

### 原因

當使用 postman 發出 post 請求時，laravel 回傳 419 | expired

laravel 會透過應用程式自動產生一個 CSRF token 來管理每一個使用者的 session。

這個 token 用於驗證已認證使用者，是否實際向應用程式發出請求。

在`vender/laravel/framework/src/Illuminate/Session/Store.php`中可以看到，每次進入 laravel 專案的時候，都會檢查 session 中\_token 是否存在，若不存在就會呼叫 `regenerateToken` 重新生成一個 token。

```php
public function start()
{
    $this->loadSession();
    if (! $this->has('_token')) {
        $this->regenerateToken();
    }

    return $this->started = true;
}
```

`regenerateToken` 實作，隨機產生亂數字元。

```php
public function regenerateToken()
{
    $this->put('_token', Str::random(40));
}
```

### postman 添加校驗 token

先進入網站首頁取得 token

將 token 放入 post request 的 header， X-XSRF-TOKEN 的欄位中。

### 撰寫 javascript 自動獲取 token

在進入網站首頁的 API 的 test 中，加入以下程式，以自動獲取 token。

```php
pm.environment.set(
    "XSRF-TOKEN",
    decodeURIComponent(pm.cookies.get("XSRF-TOKEN"))
)
```

接著在 post 的 request 中的 header 加入 `X-XSRF-TOKEN:{{XSRF-TOKEN}}`

## Postman 壓力測試(串行處理)

在要測試的 request test 中定義測試的程式碼

```javascript
// 檢查 api 是否返回 200 status code
pm.test("Status code is 200", function () {
  console.log(JSON.parse(responseBody));
  pm.response.to.have.status(200);
});
```

開啟 Runner 選擇要測試的 api

選擇 environment，調整請求次數: iterations，調整延遲時間：Delay

## http status

- 資訊回應 (informational responses) 100 - 199
- 成功回應 (Successful responses) 200 - 299
- 重定向 (Redirect) 300 - 399
- 用戶端錯誤 (Client errors) 400 - 499
- 伺服器端錯誤 (Server error) 500-599
