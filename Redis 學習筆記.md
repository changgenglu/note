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
# SET key value [EX seconds|PX milliseconds|KEEPTTL] [NX|XX]
> SET phone Note10 EX 10          # 10 秒過期
> SET price 23900

# SETNX key value
# SET if Not Exist，如果該 key 不存在才儲存
> SETNX frameworks "react vue angular" # 回傳 1 表示成功，0 表示失敗（該 key 已經存在）

# SETEX key seconds value  # 設定過期時間

# 增加或減少數值
> INCR price               # 23901，一次增加 1
> DECR price               # 23900，一次減少 1
> INCRBY price 1000        # 24900，一次增加 1000
> DECRBY price 1000        # 23900，一次減少 1000
```

### List

由於 Lists 本質上是 linked-list 的緣故，它在新增和刪除元素的速度是快的，但搜尋速度是相對慢的。可以使用 RPUSH 和 LPUSH 來新增元素，如果該 key 尚不存在的話，會回傳新的 List，如果該 key 已經存在，或它不是 List 的話，則會回傳錯誤。

- 有順序性
- 新增刪除速度相對快：適合用在只要取出頭尾元素的情況(ex: Quene)
- 搜尋速度相對慢
- 適用時機
  - Message Quene: 只需取出頭尾的元素，不需要搜尋

```redis
# 在 List 中新增元素
# RPUSH <key> <element> [element ...] / LPUSH <key> <element> [element ...]
> RPUSH frameworks react vue angular  # 3
> LPUSH frameworks svelte             # 4

# 檢視 List 中的元素
# LRANGE <key> <start> <stop>
> LRANGE frameworks 0 -1  # 列出所有元素，-1 表示 list 中的最後一個元素

# 檢視 List 數目
# LLEN <key>
> LLEN frameworks

# 移除 list 中的元素
# RPOP <key> / LPOP <key>
> RPOP frameworks         # 移除 list 最後一個元素
> LPOP frameworks
```

### Set

由多個 redis 中的 string 以無序的方式所組成，其保證內部不會有重複的元素，此外 Redis 提供了多個 Set 之間交集、差集與聯集的操作。

- 使用時機：

  - 記錄每一個造訪的 ip
  - 商品標籤

- Set 的基礎操作
  - CRUD: SADD SREM SMEMBERS SCARD SPOP
  - 集合操作: SDIFF SINTER SUNION

```redis
# SADD <key> <member> [member ...]   # 新增元素到 Set 中
> SADD languages english             # 1，新增的元素數目
> SADD languages frensh chinese      # 2，新增的元素數目
> SADD languages english             # 0，如果元素已經在該 Sets 中，會回傳 0

# SREM <key> <member> [member...]     # 從 Set 中移除元素
> SREM languages english              # 1，移除的元素數目

# SMEMBERS <key>                      # 檢視 Set 中所有元素
> SMEMBERS languages                  # 回傳的元素沒有順序性

# SISMEMBER <key> <member>            # 檢視元素是否存在該 Set 中
> SISMEMBER languages chinese         # 1，存在的話回傳 1，不存在則回傳 0

# SUNION <key> [key...]               # 合併多個 Sets
> SUNION languages programming-languages
127.0.0.1:6379> SMEMBERS bike
1) "green"
2) "white"
3) "black"
4) "red"
127.0.0.1:6379> SUNION car
1) "green"
2) "yellow"
3) "red"
127.0.0.1:6379> SUNION car bike
1) "yellow"
2) "red"
3) "white"
4) "black"
5) "green"
```

### Hash

為 key-value 的資料類型，也是 Redis 的主結構，非常適合用於儲存物件型資料，例如 User 物件有姓名、年齡、信箱等。當物件非常小時，Hash 會將資料壓縮後儲存，因此單台 redis 可以儲存數百萬個小物件。

- Hash 的基礎操作
  - CRUD: HSET HGET HDEL
  - 多欄位讀: HGETALL HKEYS HMGET

```redis
# HSET <key> <field> <value> [field value...]   # 新增 field-value pairs 到 Hash 中
> HSET phone name "iphone"       # 1，新增的數目
> HSET phone price 22500     # 1，新增的數目
> HSET phone name "iphone mini"  # 0，表示該 field 已經存在 hash 中，將會「更新」其 value

# HGET <key> <field>             # 取得 field 的 value
> HGET phone name                # "iphone mini"

# HGETALL <key>                  # 取得所該 hash 對所有值
> HGETALL phone

# HMSET <key> <field> <value> [field value...]  # 和 HSET 相同
# HMGET <key> <field> [field...]    # 一次取出多個 field 的值
> HMGET phone name priceHSET
```

### Sorted Set

為有序的 Set，其順序會依照傳入的權重值排序，在查找資料時，可使用 binary search，因此查找效率高。由於 Sorted Set 的高效能查詢，Sorted Set 可當做一組 Hash 資料的 index，將物件 id 以及 index field 儲存在 Sort Set，單筆物件的完整資料儲存在 Hash。

- 有順序性，透過 `score` 產生連結來達到排序的作用，`score` 本身會是 `float`
- 元素值仍然是唯一，但 `score` 可以不是唯一
- 不論是 Add, Remove 或是 update 速度都很快，可以同時快速搜學中間的項目
- 可以視為 `set` 和 `hash` 的混合
- 使用上指令和 `set` 相似，只要將最開頭的 `S` 改成 `Z`
- 使用時機

  - 遊戲的計分板

- Sorted Set 的基礎操作

  - CRUD: ZADD ZRANGE ZREM
  - Rank 操作: ZRANK 找元素位置，ZSCORE 設定元素權重值

- option
  - XX: 只更新存在的成員，不添加新成員
  - NX: 不更新存在的成員，只添加新成員
  - CN: 修改返回值為發生變化的成員總數，原始是返回新添加成員的總數(CH 為 change 的縮寫)。更改的元素是新增加的成員，已經存在的成員更新分數。所以在命令中指定的成員有相同的分數將不被計算在內。一般而言，ZADD 只會返回新增成員的數量
  - INCR: 當 ZADD 指定這個選項時，成員的做就等同 ZINCRBY 命令，對成員的分數進行遞增操作。

```redis
# ZADD <key> [NX|XX] [CH] [INCR] <score> <member> [score member ...]，新增 sorted Set
> ZADD students 1 aaron                  # 1
> ZADD students 2 allison         # 1
> ZADD students 3 bruce 4 derek          # 2

# XX：只更新已存在的 member 的 score，絕不新增 member
# NX：不更新已存在的 member 的 score，總是新增 member
> ZADD students XX 10 aaron    # 如果 aaron 存在，則將 score 更新為 10
> ZADD students NX 777 jen     # 如果 jen 不存在，則新增且將 score 設為 777

# ZRANGE <key> <start> <stop> [WITHSCORES]，檢視 sorted set
> ZRANGE students 0 -1                   # 檢視 sorted set 中所有元素

# ZCARD <key>，檢視該 set 中的元素數目
> ZCARD students

# ZCOUNT <key> <min> <max>     # 檢視分數介於 min ~ max 間的元素拭目
> ZCOUNT students 0 10

# ZSCORE <key> <member>        # 檢視某 member 的 score
> ZSCORE students aaron

# ZINCRBY <key> <increment> <member>    # 幫 member 的 score 分數增加
> ZINCRBY students 10 aaron             # 幫 aaron 的 score 加 10
```

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

## Redis GUI

> [Another Redis Desktop Manager](https://github.com/qishibo/AnotherRedisDesktopManager/)
>
> [[Tool] Redis 管理工具 - Another Redis Desktop Manager](https://marcus116.blogspot.com/2020/04/tool-redis-another-redis-desktop-manager.html)
