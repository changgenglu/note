# MySQL 學習筆記

###### tags: `資料庫` `MySQL`

- [MySQL 學習筆記](#mysql-學習筆記) - [tags: `資料庫` `MySQL`](#tags-資料庫-mysql)
  - [資料表語法](#資料表語法)
  - [資料型態](#資料型態)
  - [DB 命名原則](#db-命名原則)
    - [資料庫命名](#資料庫命名)
    - [資料表命名](#資料表命名)
    - [欄位命名](#欄位命名)
    - [索引命名](#索引命名)
      - [外鍵索引](#外鍵索引)
  - [使用情境](#使用情境)
    - [外鍵 onDelete 約束情況](#外鍵-ondelete-約束情況)
    - [ERROR: #1215 - Cannot add foreign key constraint](#error-1215---cannot-add-foreign-key-constraint)

## 資料表語法

- 建立資料表

  ```sql
  CREATE TABLE 資料表名稱 (
    欄位名稱  資料型態,
    ...
  )
  ```

- 增加資料表欄位

  ```sql
  ALTER TABLE 資料表 ADD 欄位名稱 資料型態;
  ```

- 增加資料表內容

  ```sql
  INSERT INTO 資料表 (欄位1, 欄位2, 欄位3, ...)
  VALUES (值1,  值2,  值3, ...);
  ```

- 更新資料欄位

  ```sql
  UPDATE 資料表 SET 欄位1 = '資料1', 欄位2 = '資料2' WHERE 條件;
  ```

- 刪除資料欄位

  ```sql
  delete from 資料表 where 欄位 = ;
  truncate table 資料表;
  drop table 資料表

  -- 刪除外鍵欄位
  ALTER TABLE 資料表 DROP FOREIGN KEY FK_欄位 ; -- 先刪除外鍵名稱
  ALTER TABLE 資料表 DROP `欄位`;               --  才能刪除欄位
  ```

- 選擇欄位

  ```sql
      SELECT 別名.欄位,
      DISTINCT 別名.欄位          -- 欄位資料不重複
      CONCAT(別名.欄位, 別名.欄位) -- 欄位合併
      FROM 資料表 as 別名         -- 別名

      join 資料表B on 資料表別名.欄位 = 資料表B別名.欄位

      WHERE 別名.欄位 = "內容" AND 別名.欄位 = 內容  -- ...而且...
      WHERE (別名.欄位 = "內容" OR 別名.欄位 = 內容) -- (..或者..)
                                                  -- OR的前後要加上括弧，將條件限制住
      WHERE 別名.欄位 LIKE "%內容%"                 -- 內容部分有符合者
      WHERE 別名.欄位 BETWEEN 起始 and 結束         -- 連續不間段的區間
      WHERE 別名.欄位 in (內容, 內容)               -- 挑選同一型態指定內容

      GROUP BY                -- 相同的東西記錄在一起
      HAVING                  -- 接在 GROUP BY 之後設定條件
      UNION
      ORDER BY 別名.欄位       -- 排序(預設由小到大)
      ORDER BY 別名.欄位 DESC  -- 排序由大到小
      LIMIT 起始位置, 資料筆數; -- 選擇資料中要顯示的項目
  ```

- 使用者定義變數

  ```sql
  SELECT @x; --> x值為NULL

  SET @x = 1;
  SELECT @x, ProductID  FROM Products;
  ```

- 子查詢:查詢裡面有查詢

  ```sql
  --> 檢查資料表302中的 ProductID，不存在於資料表301 的 ProductID
  SELECT * FROM lab302
  WHERE ProductID NOT in (SELECT ProductID FROM lab301)
  ```

- inner join | left join | right join | cross join

  ```sql
  -- 結合兩個表中某欄位具有相同資料，一起列出查詢結果
  inner join
  -- 列出左/右邊的表，另一邊的表列出有相同的部分，不足的欄位印出NULL
  left join | right join
  -- 將所有可能的組合通通列出來(交叉查詢)
  cross join
  ```

## 資料型態

- 字串類型

  - `char()` 與 `varchar()` 的空間大小是以後面參數來表示欄位的大小，不同的地方在於`varchar()` 是以動態的方式儲存。

    ```sql
    char(10) = "hello     " -- 10 bytes  包含了五個空格
    varchar(10) = "hello"   -- 5 bytes
    ```

  - `char()` 固定大小浪費空間，但是所需的計算時間少。
  - `varchar()` 不固定長度，但是每一次抓取都要運算，花費 CPU 運算時間

- 數值類型

  |    type     | storage(bytes) |          signed          |    unsigned    |
  | :---------: | :------------: | :----------------------: | :------------: |
  |  `TINYINT`  |       1        |        -128 ~ 127        |    0 ~ 225     |
  | `SMALLINT`  |       2        |      -32768 ~ 32767      |   0 ~ 65535    |
  | `MEDIUMINT` |       3        |    -8388608 ~ 8388607    |  0 ~ 16777215  |
  |    `int`    |       4        | -2147483648 ~ 2147483647 | 0 ~ 4294967295 |
  |  `BIGINT`   |       8        |  $-2^{63}$ ~ $2^{63}-1$  | 0 ~ $2^{64}-1$ |

- `DECIMAL(x, y)` : x = 數值長度(包含小數點)，y = 小數點後的位數(不足補零)

- 時間類型

  - `YEAR` : YYYY
  - `TIME` : HH:MM:SS
  - `DATE` : YYYY-MM-DD
  - `DATETIM`E : YYYY-MM-DD HH:MM:SS

- 鍵名

  - `primary key` 主索引鍵(主鍵)
  - `foreign key` 外部索引鍵(外來鍵)
  - `UNIQUE` 唯一 不能有重複的資料
  - `_INCREMENT` 流水號
  - `DEFAULT =` 預設值
  - `CHECK ()` 資料寫入前的檢查(預設標準)

- functions | method | 方法 | 函式 | 副程式 | 函數

  - `count()` : 計算數量
  - `MAX()` : 找最大的那一個
  - `AVG()` : 平均值
  - `ABS()` : 取絕對值
  - `ROUND()` : 小數點四捨五入

  ***

  - 取得現在時間
    - `CURRENT_DATE()`
    - `SYSDATE()`
    - `NOW()`

  ***

  - `Year()` : 年

  - `Month()` : 月

  - `DAY()` : 日

  - `LENGTH()` : 資料的大小 bytes

  - `CHAR_LENGTH(`) : 字串的長度

  ***

  - `POWER(數值, N次方)` : 計算次方

  - `SUBSTRING(欄位, 起始位置, 擷取長度)` : 擷取字串

  - `INSTR(欄位, '指定的文字')` : 找出指定位置的位置，回傳數值

  - `LEFT(欄位, 擷取長度)` : 從左邊開始擷取到指定長度

  - `REPLACE(目標欄位, '目標字串', '要取代上字串')` :取代指定字元

  - `RPAD(內容, 內容的長度, '取代的字')` : 內容不足或是超過的部分會被取代

  - `REPEAT('要重複的字', 重複次數)` :重複輸入

  ***

  - 將字串轉換型態
    - `CONVERT('字串', 型態)`
    - `CAST('字串' AS 型態)`

  ***

  - `DATE_FORMAT(日期, "%Y")` :日期格式，取得日期中的項目

  ***

  - `IF(判斷條件, "條件為T", "條件為F")` :條件判斷為 true 返回 1，否則返回 2

  - `ELT(數值、清單, '值1', '值2'......'值n')` :透過數值清單傳回指定之索引的項目

  - `IFNULL(x, y)` : 如果 x 有值回傳 x，如果 x 為 NULL 回傳 y

  - `ISNULL(x)` : 如果 x 為 NULL，ISNULL(x)會回傳 1，否則回傳 0

  - `NULLIF(x, y)` : 如果 x = y 回傳 NULL，否則回傳 x

  ***

  - 類似 if else

    ```sql
    CASE
      when condition(條件為true) then "返回結果"
      --如果條件為false就繼續下一行判斷
      when condition(條件為true) then "返回結果"
      else "返回結果" -- 如果上述條件都不符，就返回此結果
    end
    ```

  - 不等於：

    - `<>`
    - `!=`
    - `NOT`

  - 比較：
    - `>=`
    - `<=`

## DB 命名原則

- 命名只能使用英文字母、數字、下劃線，以英文字母開頭
- 避免用 MySQL 的保留字如：backup、call、group 等
- 所有資料庫物件使用小寫字母

### 資料庫命名

- 不超過 30 個字元

### 資料表命名

- 一律使用複數名詞
- 不超過 30 個字元
- 多對多關係中的中間表命名，為兩個表名稱，中間以`_`區額`，以單數命名 例如：`admins`和`members`，中間表命名為`admin_member`

### 欄位命名

- 各表之間相同意義的欄位必須同名
- 多單詞以`_`
- 外鍵約束欄位，以關聯的父層資料表名加上父層資料表欄位名來命名，中間以`_`區隔  
  例:父層資料表名`admins`，父層資料表欄位名`id`，關聯欄位名`admin_id`

### 索引命名

#### 外鍵索引

- 資料表名稱\_關聯欄位名稱\_foreign

## 使用情境

### 外鍵 onDelete 約束情況

- 沒有加入`onDelete`  
  如果在關聯中的限制屬性，沒有加入`onDelete`，此時刪除外鍵約束的父層資料表中的欄位，會出現#1451 error

  ```cmd
  #1451 - Cannot delete or update a parent row: a foreign key constraint fails (`test0505`.`posts`, CONSTRAINT `posts_user_id_foreign` FOREIGN KEY (`user_id`)     REFERENCES `users` (`id`))
  ```

- onDelete('set null')
  刪除父層資料表的欄位時，同時會將關聯的子資料表中的欄位設為`null`。
- onDelete('cascade')
  刪除父層資料表的欄位時，同時會將關聯的子資料表中的欄位刪除。

### ERROR: #1215 - Cannot add foreign key constraint

- 可能原因:
  1. 添加外鍵約束時，目標欄位須和引用欄位具有相同的數據類型，int signed with int signed 或 int unsigned with int unsigned
  2. 在 not null 的欄位加上 on delete/update set null 的外鍵約束，須將該欄位設為 DEFAULT NULL

### 刪除重複的資料

#### 使用 `DISTINCT` 去除重複值

需求：查找 `02:81:85:34:ED:DC` 表中的數據，將表中 `i`, `p`, `ep`, `eq`, `pf`, `created_at` 這六個欄位均重複的資料刪除，並重新整理 id

先建立一個表，接者使用 `SELECT DISTINCT` 去除重複的值，並把去除重複值的資料，存入新資料表中

```sql
CREATE TABLE `02:81:85:34:ED:DC_copy` (
  `id` int(10) UNSIGNED NOT NULL,
  `i` json DEFAULT NULL,
  `p` json DEFAULT NULL,
  `ep` json DEFAULT NULL,
  `eq` json DEFAULT NULL,
  `pf` json DEFAULT NULL,
  `created_at` datetime DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `02:81:85:34:ED:DC_copy` (`i`, `p`, `ep`, `eq`, `pf`, `created_at`)
SELECT DISTINCT `i`, `p`, `ep`, `eq`, `pf`, `created_at` FROM `02:81:85:34:ED:DC`;
```

最後刪掉原表，並將複製的表改名

```sql
drop tables `02:81:85:34:ED:DC`;
alter table `02:81:85:34:ED:DC_copy` rename to `02:81:85:34:ED:DC`;
```

### 匯入 txt 檔

建立資料表

```sql
create table `city_raw_data` (
    `geonameid` int(10) NOT NULL,
    `name` varchar(200) DEFAULT NULL,
    `latitude` decimal(11, 8) DEFAULT NULL,
    `longitude` decimal(11, 8) DEFAULT NULL,
    `country code` varchar(10) DEFAULT NULL,
    `timezone` varchar(40) DEFAULT NULL,
    `modification date` date DEFAULT NULL
) ENGINE = InnoDB DEFAULT CHARSET = utf8;
```

本地 txt 檔匯入
在 txt 檔中，每一個欄位用 tab 鍵進行分隔

```sql
LOAD DATA INFILE "C:/Users/RD/Desktop/ES.txt" INTO TABLE `city_raw_data` (
`geonameid`,`name`,`asciiname`,`alternatenames`,`latitude`,`longitude`,`feature class`,`feature code`,`country code`,`cc2`,`admin1 code`,`admin2 code``admin3 code`,`admin4 code`,`population`,`elevation`,`dem`,`timezone`,`modification date`
)
```

或用指定的符號進行分隔，如：`|`

```sql
LOAD DATA INFILE "C:/Users/RD/Desktop/ES.txt" INTO TABLE `city_raw_data` FIELDS TERMINATED BY '|' (
    `geonameid`,
    `name`,
    `latitude`,
    `longitude`,
    `country code`,
    `timezone`,
    `modification date`
);
```

### 複合主鍵與聯合主鍵，索引與聯合(複合)索引

#### 複合主鍵與聯合主鍵

- **複合主鍵**：表的主鍵含有一個以上的欄位組成，不使用無業務含意的自增 id 作為主鍵

```sql
create table test
(
  'name' varchar(19) NOT NULL
  'id' number
  'value' varchar(10)
  primary key ('name', 'id')
)
```

`name` 和 `id` 欄位組合起來就是 test 表的複合主鍵，他的出現是因為 `name` 欄位可能會出現重複，所以要加上 `id` 欄位，如此可以保證紀錄的唯一性。

一般而言，主見的欄位資料長度和字串字數越少越好。

當表中只有一個主鍵時，他是唯一的索引；當表中有多個主鍵時，稱為複合主鍵，複合主鍵聯合保證唯一索引。

某者個主鍵欄位值出現重複是沒問題的，只要不是有多筆資料的所有主鍵值完全一樣，就不算重複。

- **聯合主鍵**：多個主鍵聯合形成一個主鍵組合。

聯合主鍵的意義：用兩個字串(或多字串)來確定一條紀錄。這兩個字串都不唯一，可以分別重複。

如：主鍵 A 和主鍵 B 組成聯合主鍵

主鍵 A 跟主鍵 B 的資料可以完全相同，聯合就在於主鍵 A 和主鍵 B 形成的聯合主鍵是唯一的。
下面的例子：主鍵 A 的數據是 1，主鍵 B 的數據也是 1，聯合主鍵其實是 11，11 是唯一值，不允許再出現 11 這個值。(即為多對多關係)

| 主鍵 A | 主鍵 B |
| :----: | :----: |
|   1    |   1    |
|   2    |   2    |
|   3    |   3    |

主鍵 A 與主鍵 B 的聯合主鍵值最多為：11, 12, 13, 21, 22, 23, 31, 32, 33

#### 索引、聯合(複合)索引

- **索引**：加快查詢速度的有效手段。

  系統讀寫資料時，會自動選擇合適的索引作為存取路徑。

  建立索引

  ```sql
  -- 建立索引
  create [unique][cluster]index<索引名>

  -- 刪除索引
  drop index <索引名>
  ```

  索引是各種關係資料庫中，最常見的一種邏輯單元，是關係資料庫系統很重要的組成部分，對於提高檢索資料速度有很顯著的效果。
  索引的原理是利用根據索引值得到行指針，然後快速定位到資料庫紀錄。

  索引的使用：

  1. 主鍵(預設自帶索引)和外鍵(以及一些可以跟其他表觀聯的字段)
  2. where 子句中基常出現的字段。
  3. 索引應該建立在小字段上，大數據字段(bit, image, test, blob)等，不適用

- **聯合(複合)索引**

```sql
-- 建立聯合索引
create index <索引名> on 表名(欄位1, 欄位2, ...);

-- 例如：有聯合索引 (a, b, c, d)
select * from test where a=1 and b=2; -- a 和 b 索引都起作用
```

索引生效原則：按照聯合索引的順序，由前往後依次使用生效，如果中間某個索引沒有使用，那麼斷點前面的索引部份起作用，斷點後面的索引沒有起作用。

例如

```sql
where a=1 and b=2 and d=4;
```

則只有 a 和 b 索引起作用，中間 c 斷了，則 d 索引不起作用。

mysql 會一值向右尋找直到遇到範圍查詢(>, <, between, like)時停止。

如：

```sql
where a = 1 and b = 2 and c > 3 and d = 4 ;
```

若建立 (a, b, c, d)順序的索引，則索引範圍不會到 b ，因為 c 出現 ">"。
如果建立(a, b, d, c)的順序，則

```sql
where a = 1 and b = 2 and c > 3 and d = 4
```

其中 abcd 都可以用到索引，並且 abd 的順序可以任意調整。

```sql
select * from test where b=2 and d=4 and a=1and c>3;
```

以上 sql 語句中，a, b, c, d 都用到了索引

**注意**

1. 只要列中包含有 null 值，都將不會被包含在索引中，複合索引中只要有一列含有 null 值，那此列的複合索引就是無效的，因此在資料庫設計時盡量不要讓欄位的預設值為 null
2. 使用 like 關鍵字須注意：`like "%aaa%"` 不會命中索引，`like "aaa%"` 才會命中索引。
3. NOT IN 和操作都會變成全表掃描，not in 可以用 not exist 代替。
