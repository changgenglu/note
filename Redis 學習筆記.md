# Redis 學習筆記

## 資料類型

- redis 支援五種資料類型：
  - string 字串
  - hash 雜湊
  - list 列表
  - set 集合
  - zset 有序集合

### string

一個 key 對應一個 value。

字串類型為二進制，因此 string 可包含任何資料，如 jpg 圖片，或是一個序列化的物件。

string 最大可以儲存 512MB

```redis
redis 127.0.0.1:6379> set test "測試測試"
OK
redis 127.0.0.1:6379> get test
"測試測試"
```

#### 指令

| 指令          | 說明                    |
| ------------- | ----------------------- |
| SET key value | 設定指定的 key 和 value |
| GET key       | 取得指定 key 的 value   |


## redis Key

| 指令                                      | 描述                                      |
| ----------------------------------------- | ----------------------------------------- |
| DEL key                                   | 當 key 存在時，將其刪除                   |
| DUMP key                                  | 序列化傳入的 key，並回傳被序列化的值      |
| EXISTS key                                | 檢查傳入的 key 是否存在                   |
| EXPIRE key seconds                        | 為傳入的 key 設定過期時間，以秒計         |
| EXPIREAT key timestamp                    | 設定過期時間，接受 UNIX 時間戳 為時間參數 |
| PEXPIRE key milliseconds                  | 設置過期時間以毫秒計                      |
| PEXPIREAT key milliseconds-timestamp      | 設置過期時間的時間戳以毫秒計              |
| KEYS pattern                              | 查找所有符合傳入模式 (pattem) 的 key      |
| MOVE key db                               | 將目前資料庫中 key 移動到指定的資料庫中   |
| PERSIST key                               | 移除 key 的過期時間，key 將永久保存       |
| PTTL key                                  | 以毫秒為單位，回傳 key 剩餘的過期時間     |
| TTL key                                   | 以秒為單位，回傳 key 剩餘的過期時間       |
| RANDOMKEY                                 | 從資料庫中，隨機回傳一個 key              |
| RENAME key newkey                         | 修改 key 的名稱                           |
| RENAMENX key newkey                       | 當 newkey 不存在時，將 key 改名為 newkey  |
| SCAN cursor [MATCH pattern] [COUNT count] | 迭代資料庫中的資料庫鍵                    |
| TYPE key                                  | 返回 key 所儲存的值的類型                 |
