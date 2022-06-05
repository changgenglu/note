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
- token 驗證 ｜token 型驗證

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
