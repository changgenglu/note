# Carbon 學習筆記

> php 常用處理時間格式的套件
>
> 在 laravel 5.0 之後為預設套件
>
> 底層是使用 PHP `Datetime` 的 `strtotime` 方法
>
> ---
>
> 參考資料：
>
> [Carbon 使用技巧整理 (上)](https://reurl.cc/eWWbLm)
>
> [Carbon 使用技巧整理 (下)](https://reurl.cc/X55rGD)
>
> [Laravel 5 學習筆記 - Carbon 時間套件](https://www.kancloud.cn/kancloud/laravel-5-learning-notes/50163)

## 目錄

- [Carbon 學習筆記](#carbon-學習筆記)
  - [目錄](#目錄)
  - [物件建立](#物件建立)
    - [建立當下時間](#建立當下時間)
    - [解析並產生時間物件](#解析並產生時間物件)
    - [依規則建立](#依規則建立)
    - [Bug 與安全模式](#bug-與安全模式)
  - [時間格式(時區、本地化)](#時間格式時區本地化)
    - [本地語系](#本地語系)
    - [Fromat](#format)
  - [Getter \& Setter](#getter--setter)
    - [時間屬性](#時間屬性)
    - [時間與語系](#時間與語系)
    - [Setter](#setter)
  - [比較](#比較)
    - [大於、小於和等於](#大於小於和等於)
    - [之間、最小最大與最近最遠](#之間最小最大與最近最遠)
    - [和今天比較](#和今天比較)
  - [時間運算](#時間運算)
    - [Overflow 溢出](#overflow-溢出)
  - [差異](#差異)
    - [一般差異](#一般差異)
    - [Real Difference](#real-difference)
  - [口語化修改器](#口語化修改器)

## 物件建立

### 建立當下時間

```php
$now = new Carbon();
$now = Carbon::now();
$now = new Carbon('first day of January 2022', 'America/Vancouver');  //  2022-01-01 00:00:00.0 America/Vancouver (-08:00)
```

- 直接建立可以解析文字，且指定時區

### 解析並產生時間物件

```php
Carbon::parse('first day of December 2022');    // 2022-12-01 00:00:00.0 UTC (+00:00)
Carbon::parse('2022/4/01');                     // 2022-04-01
Carbon::parse('2022/04/01');                    // 2022-04-01
Carbon::parse('2022-04-01');                    // 2022-04-01
```

### 依規則建立

```php
Carbon::create($year, $month, $day, $hour, $minute, $second, $tz);  // 通用變數順序
Carbon::createFromDate($year, $month, $day, $tz);
Carbon::createFromTime($hour, $minute, $second, $tz);
Carbon::createFromTimeStamp('1601735792.198956');                   // 2020-10-03 14:36:32.198956
```

- 若傳入的資料不完整，亦能產生時間物件，預設為最小值
- 也可以用 `parse` 的方式操作 `create`
- 若不帶值，則會產生 `0001-01-01`

```php
Carbon::create($year, $month);  // 2020-10-03 00:00:00
Carbon::create('2022/4/01');    // 2022-04-01 00:00:00.0
Carbon::create();               // 0000-01-01
```

### Bug 與安全模式

- `createFrom()` 其預設帶值的特性是取的當下時間，因此若在大月的月底使用，容易有跨月的問題

```php
Carbon::createFromDate(2022, 4);    // 未帶入的參數，會預設為當下的時間
// 當下為 30 號：2022-04-30
// 若為 31 號：2022-05-01
```

- 使用 `createSafe()` 安全模式建立，會幫忙檢查其合理性。若傳入不合法的數值，會回傳 Exception。

```php
Carbon::create(2022, 1, 35);        // 2022-02-04
Carbon::createSafe(2022, 1, 35);    //  throw InvalidDateException
```

## 時間格式(時區、本地化)

| 單位 | 年  | 月  | 日  | 星期 | 時(24) | 時(12) | 分  | 秒  | 毫秒 | 時區 |
| :--: | :-: | :-: | :-: | :--: | :----: | :----: | :-: | :-: | :--: | :--: |
| 縮寫 |  Y  |  M  |  D  |  d   |   H    |   h    |  m  |  s  |  S   |  z   |

### 本地語系

- Laravel 程式預設

```php
$date = Carbon::now()->locale('zh-tw');
$date->diffForHumans();     // 1 秒前
$date->monthName;           // 十一月
$date->isoFormat('LLLL');   // 2022年11月17日星期四 02:01
```

- 手動設定

```php
// 通用設定
$factory = new Factory([
    'locale' => 'zh-tw',
    'timezone' => 'Asia/Taipei'
]);
$factory->now();

// 暫時設定
Carbon:now()->settings([
    'locale' => 'fr-FR',
    'timezone' => 'Europe/Paris'
]);
```

### Format

```php
Carbon::now()->format('Y-m-d H:m:s l');             // 2022-11-17 02:11:55 Thursday
Carbon::now()->translatedFormat('Y-m-d H:m:s l');   // 2022-11-17 02:11:55 星期四
```

- 除了利用 `format()` 輸出時間格式，也可以使用 `toDateString()`, `toTimeString()`, `toISOString()` 等方法，將時間物件轉換成字串，亦有提供 `toArray()`, `toObject()` 方法。

```php
Carbon::hasFormat('2022-11-11', 'Y-m-d')                // true
Carbon::hasFormat('2022~11~11', 'Y~m~d')                // true
Carbon::hasFormatWithModifiers('2022-11-11', 'Y#m#d')   // true
Carbon::hasFormatWithModifiers('2022/11/11', 'Y#m#d')   // true
Carbon::hasFormatWithModifiers('2022~11~11', 'Y#m#d')   // false
```

- 利用 `hasFormat()` 來判斷 `format` 格式
- `hasFormatWithModifiers()` 為模糊比對，可以用 `#` 區隔時間縮碼，可以同時判斷 `/`, `-`

## Getter & Setter

### 時間屬性

```php
$date = Carbon::parse('2022-02-28');
$date->year;        // 2022
$date->month;       // 2
$date->dayOfYear;   // 59
$date->dayOfMonth;  // 28
```

### 時間與語系

- 判斷時間事件的語系有沒有符合應用程式，及有沒有使用 UTC 國際標準時區

```php
Carbon::now()->local;                       // bool(true)，檢查時區符合應用程式
Carbon::now('America/Vancouver')->local;    // bool(false)
Carbon::now()->utc;                         // bool(false)，檢查標準時區
```

### Setter

- 直接依單位設定時間

```php
$date = Carbon::now();
$date->setYear(2005);
$date->year;           // 2005
```

```php
$sourcel = new Carbon('2010-05-16 22:40:10.1');
$date-> new Carbon('2001-01-01 01:01:01.2');
$date->setTimeFrom($sourcel);   // 2001-01-01 22:40:10
```

- `setTimeFrom()` 保持日期，只設定時分秒
- `setDateFrom()`
- `setDateTimeFrom()`
- **注意！以上作法不會改變時區**

## 比較

### 大於、小於和等於

```php
$first = Carbon::create(2022, 11, 11, 0, 0, 0);
$second = Carbon::create(2022, 11, 11, 0, 0, 0, 'America/Vancouver'); // 時區設定

$first->equalTo($second); // false
$first->notEqualTo($second); // true
```

```php
$first = Carbon::parse('2022-11-11 00:00:00');
$second = Carbon::parse('2022-11-11 12:00:00');

$first->greaterThan($second);           // false  大於
$first->greaterThanOrEqualTo($second);  // false 大於等於
$first->gt($second);                    // 可以使用縮寫
$first->gte($second);
$first >= $second;                      // 也可以使用運算式

$first->lessThan($second);              // true  小於
$first->lessThanOrEqualTo($second);     // true 小於等於
$first->lt($second);
$first->lte($second);
$first <= $second;
```

### 之間、最小最大與最近最遠

```php
$first = Carbon::parse('2022-11-11');
$second = Carbon::parse('2022-11-15');
$data = Carbon::parse('2022-11-15');
$data->between($first, $second);        // true
$data->between($first, $second, false); // 嚴格模式 false
```

```php
$first = Carbon::parse('2022-11-11');
$second = Carbon::parse('2022-11-15');

$first->min($second);   // '2022-11-11'
$first->max($second);   // '2022-11-15'

$data = Carbon::parse('2022-11-14');
$data->closest($first, $second);    // 2022-11-15
$data->farthest($first, $second);   // 2022-11-11
```

- `closest` 和 `farthest` 函式可以找出兩個時間物件中，與特定時間物件最接近或最遙遠的物件

### 和今天比較

```php
// 假設今天是 2022-11-10
$date = Carbon::parse('2022-11-11');

$date->isSameWeek();    // true
$date->isSameMonth();   // true
$date->isCurrentDay();  // false
```

- 另外還有 `isMonday()` `isTomorrow()` `isToday()`，`isBirthday()` 可以判斷是否為不同年的同一天

## 時間運算

**重要觀念**：carbon 物件在運算過程中會改變原始物件，因此在運算之前要加上 `copy()` 語法。

```php
$date = Carbon::create(2022, 11, 11);
$date->copy()->addDay();    // 2022-11-12
// 複數運算，時間單位要加上 s
$date->addDays(2);          // 2022-11-13
print_r($date);             // 2022-11-13

$date->copy()->subDay(); // 2022-11-12
```

### Overflow 溢出

```php
$date = Carbon::create(2022, 1, 31);
$date->copy()->addMonth(); // 2022-03-03
```

- 一般而言，加上一個月後應該要是二月底(2/28 或 2/29)，為避免不合理的狀況發生，須加上 `NoOverflow` 關鍵字，讓運算結果不會溢出

```php
$date = Carbon::create(2022, 1, 31);
$date->copy()->addMonthNoOverflow(); // 2022-02-28
$date->copy()->subMonthNoOverflow(2); // 2022-11-30
```

另外亦可以使用 `addUnitNoOverflow` 針對溢出的單位做控管，也可以在設定值，規定不可以溢出

```php
$date = Carbon::parse('2022-01-31');

// UnitNoOverflow(運算單位，值，限制防溢出單位)
$date->copy()->addUnitNoOverflow('hour', 7, 'day') // 07:00
$date->copy()->addUnitNoOverflow('hour', 48, 'day') // 23:59

Carbon::parse('2022-02-01')->copy()->setUnitNoOverflow('day', 31, 'month'); // 2022-02-28
```

## 差異

### 一般差異

- 無條件捨去，滿一個時間單位，才會回傳 1

```php
$date->diffInMinths($date->copy()->addMonthNoOverflow()); // 1
```

- 除了整數的差異，也可以比對到小數點單位的時間差

```php
Carbon::parse('06:01:23')->floatDiffInminutes('06:02:34');              // 1.1833
Carbon::parse('2022-01-01 12:00')->floatDiffInDays('2022-02-11 06:00')  // 40.75
```

- 不建議用做計算月份，會有 bug

```php
Carbon::parse('2022-01-31')->floatDiffInmonthss('2022-03-01');
// 一般而言應回傳 1.xx，但實際回傳 0.9xx 因為跨到二月不足三十天
```

### Real Difference

```php
$date = new Carbon('2014-03-30 00:00:00', 'Europe/London');]
$date->addRealHours(25);      // 2014-03-31 02:00:00.0 Europe/London (+01:00)

$date->diffInRealHours('2014-03-30 00:00:00');  // 25
$date->diffInHours('2014-03-30 00:00:00');      // 26
```

- 運算時，若出現跨日的情形，時間物件的時區會自動針對該值做增減，而在比較兩個時間點的差異時，若未用 `real` 則會計算出表面的時間差。

## 口語化修改器

```php
$date = Carbon::parse('2022-11-11 12:00:00');
$date->startOfDay();    // 2022-11-11 00:00:00
$date->endOfDay();      // 2022-11-11 23:59:59

$date->startOfMonth();  // 2022-11-01 00:00:00
$date->endOfMonth();    // 2022-11-30 23:59:59
```

- 亦支援 `today()`, `yesterday()`, `next()`
