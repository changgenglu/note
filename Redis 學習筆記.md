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

### List

由多個 redis 的 string 所組成，List 中成員會依照插入的順序儲存，期提供由頭尾插入，以及頭尾拿出的功能。List 實作的功能很多，例如任務列隊，以及優先權列隊等。

- List 的基礎操作
  - 插入資料: LPUSH RPUSH LPOP RPOP
  - 資料查詢: LRANGE LLEN LPOS(找元素的 index)
  - 阻塞操作: BLPOP BRPOP 如果 List 中沒有資料，Redis 會 blocking
  - LTRIM: 根據輸入的 range 保留 List
  - LREM: 根據元素出現次數移除多個元素。

### Set

由多個 redis 中的 string 以無序的方式所組成，其保證內部不會有重複的元素，此外 Redis 提供了多個 Set 之間交集、差集與聯集的操作。

- Set 的基礎操作
  - CRUD: SADD SREM SMEMBERS SCARD SPOP
  - 集合操作: SDIFF SINTER SUNION

### Hash

為 key-value 的資料類型，也是 Redis 的主結構，非常適合用於儲存物件型資料，例如 User 物件有姓名、年齡、信箱等。當物件非常小時，Hash 會將資料壓縮後儲存，因此單台 redis 可以儲存數百萬個小物件。

- Hash 的基礎操作
  - CRUD: HSET HGET HDEL
  - 多欄位讀: HGETALL HKEYS HMGET

### Sorted Set

為有序的 Set，其順序會依照傳入的權重值排序，在查找資料時，可使用 binary search，因此查找效率高。由於 Sorted Set 的高效能查詢，Sorted Set可當做一組Hash 資料的 index，將物件 id 以及 index field 儲存在 Sort Set，單筆物件的完整資料儲存在 Hash。

- Sorted Set 的基礎操作
  - CRUD: ZADD ZRANGE ZREM
  - Rank 操作: ZRANK找元素位置，ZSCORE設定元素權重值

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
