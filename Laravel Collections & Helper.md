# Laravel Collections & Helper

- [Laravel Collections \& Helper](#laravel-collections--helper)
  - [Collections 集合](#collections-集合)
    - [運算類](#運算類)
      - [Integer](#integer)
        - [`count()` 計數](#count-計數)
      - [Boolean](#boolean)
        - [`contains()` 判斷集合是否包含指定的條件](#contains-判斷集合是否包含指定的條件)
      - [Array](#array)
        - [`diff()` 比較集合或陣列的值](#diff-比較集合或陣列的值)
    - [跌代類](#跌代類)
      - [`filter()` 傳入匿名函式篩選集合](#filter-傳入匿名函式篩選集合)
      - [`map()` 遍歷集合](#map-遍歷集合)
    - [分組類](#分組類)
      - [`countBy()` 計算指定數值](#countby-計算指定數值)
    - [變形類](#變形類)
      - [維度與順序](#維度與順序)
        - [`collapse()` , `flatten()`](#collapse--flatten)
        - [`sort()` 將陣列重新排列](#sort-將陣列重新排列)
      - [組合](#組合)
        - [`combine()` 將一個集合的值作為 key，用來和另一陣列或集合的值進行組合](#combine-將一個集合的值作為-key用來和另一陣列或集合的值進行組合)
        - [`merge()` 合併指定的陣列或是集合到原集合](#merge-合併指定的陣列或是集合到原集合)
        - [`concat()` 將傳入的值追加到集合的末端](#concat-將傳入的值追加到集合的末端)
        - [`push()` 把指定的值加入集合的末端](#push-把指定的值加入集合的末端)
        - [`prepend()` 將指定的值加入集合的開頭](#prepend-將指定的值加入集合的開頭)
      - [擷取](#擷取)
        - [`except()` 擷取除了 a, b 以外的](#except-擷取除了-a-b-以外的)
        - [`only()` 只擷取 a, b](#only-只擷取-a-b)
        - [`get()` 取得特定 key 的值](#get-取得特定-key-的值)
        - [`forget()` 直接刪除指定 key 值對應的 value](#forget-直接刪除指定-key-值對應的-value)
        - [`pull()` 和 `get()` 雷同，會修改原本的 collection](#pull-和-get-雷同會修改原本的-collection)
        - [`pluck()` 取得集合中所有陣列的 key 值](#pluck-取得集合中所有陣列的-key-值)
        - [`intersect` 從原集合中移除在指定陣列中或集合中不存在的值](#intersect-從原集合中移除在指定陣列中或集合中不存在的值)
        - [`keys()`, `values()` 取出集合中的 key 或 value](#keys-values-取出集合中的-key-或-value)
      - [轉型](#轉型)
        - [`toArray()`, `toJson()` 提供轉成陣列或是 json 等常用格式](#toarray-tojson-提供轉成陣列或是-json-等常用格式)
    - [Where](#where)
      - ['first' 返回陣列中指令條件的第一元素](#first-返回陣列中指令條件的第一元素)
    - [軟體操作類](#軟體操作類)
      - [`collect()` 複製一個新的 collection，記憶體位置不衝突](#collect-複製一個新的-collection記憶體位置不衝突)
  - [Helper 輔助函數](#helper-輔助函數)
    - [`Arr::add()` 將數值加入陣列](#arradd-將數值加入陣列)
    - [`after()` 返回傳入的字串的值後面所有的內容](#after-返回傳入的字串的值後面所有的內容)

## Collections 集合

> 參考資料
>
> [Laravel — Collection 用途大解析](https://medium.com/johnliu-%E7%9A%84%E8%BB%9F%E9%AB%94%E5%B7%A5%E7%A8%8B%E6%80%9D%E7%B6%AD/laravel-collection-%E7%94%A8%E9%80%94%E5%A4%A7%E8%A7%A3%E6%9E%90-1e2dbc6e2c93)

### 運算類

方便進行數學運算，或是判斷是否含有特定值，也能比較兩個 collection 之間的不同

#### Integer

##### `count()` 計數

```php
$collection = collect([1, 2, 3, 4]);

$collection->count();

// 4
```

#### Boolean

##### `contains()` 判斷集合是否包含指定的條件

傳入值

```php
$collection = collect(['name' => 'Desk', 'price' => 100]);

$collection->contains('Desk');

// true

$collection->contains('New York');

// false
```

傳入陣列

```php
$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->contains('product', 'Bookcase');

// false
```

傳遞匿名函數

```php
$collection = collect([1, 2, 3, 4, 5]);

$collection->contains(function ($value, $key) {
    return $value > 5;
});

// false
```

- `containsStrict()` 方法和 `contains()` 類似，但他在比較時更嚴格。

#### Array

##### `diff()` 比較集合或陣列的值

返回不存在此方法參數中的值

```php
$diff = collect([1, 2, 3, 4, 5])->diff([2, 4, 6, 8]);
$diff->all();

// 返回[1, 3, 5]，原集合內與diff方法中相同的數值被剔除。
```

### 跌代類

#### `filter()` 傳入匿名函式篩選集合

返回通過篩選的項目

```php
$filtered = collect([1, 2, 3, 4])->filter(function (item) {
    return $item > 2
});

$filtered->all();

// [3, 4]
```

#### `map()` 遍歷集合

將集合的值透過傳入的匿名函數修改並返回，生成修改過的新集合

```php
$collection = collect([1, 2, 3, 4, 5]);

$multiplied = $collection->map(function ($item, $key) {
    return $item * 2;
});

$multiplied->all();

// [2, 4, 6, 8, 10]
```

### 分組類

#### `countBy()` 計算指定數值

```php
$collection = collect([1, 2, 2, 2, 3]);

$counted = $collection->countBy();

$counted->all();

// [1 => 1, 2 => 3, 3 => 1]
```

可以傳入匿名函數來自訂要計數的值

```php
$collection = collect(['alice@gmail.com', 'bob@yahoo.com', 'carlos@gmail.com']);

$counted = $collection->countBy(function ($email) {
    return substr(strrchr($email, "@"), 1);
});

$counted->all();

// ['gmail.com' => 2, 'yahoo.com' => 1]
```

### 變形類

將 collection 改變資料結構

#### 維度與順序

##### `collapse()` , `flatten()`

兩者都是將維度從多維降為一維，collapse 較適用 Array 形式的資料，flatten 更適合有 Key-Value 形式的資料。
兩個函式的用途，都是將例如 [[1,2,3], [4,5,6]] 轉變成 [1,2,3,4,5,6]

##### `sort()` 將陣列重新排列

```php
use Illuminate\Support\Arr;

$array = ['Desk', 'Table', 'Chair'];

$sorted = Arr::sort($array);

// ['Chair', 'Desk', 'Table']
```

根據傳入匿名函數返回的結果，對陣列進行排序

```php
use Illuminate\Support\Arr;

$array = [
    ['name' => 'Desk'],
    ['name' => 'Table'],
    ['name' => 'Chair'],
];

$sorted = array_values(Arr::sort($array, function ($value) {
    return $value['name'];
}));

/*
    [
        ['name' => 'Chair'],
        ['name' => 'Desk'],
        ['name' => 'Table'],
    ]
*/
```

利用 key 值替陣列排序

```php
$list = [
  5 => 1
  4 => 2
  2 => 4
  1 => 5
  6 => 6
];

Arr::sort($list, function ($value, $key) {
    return $key;
})
```

輸出

```log
array:5 [
  1 => 5
  2 => 4
  4 => 2
  5 => 1
  6 => 6
]
```

#### 組合

##### `combine()` 將一個集合的值作為 key，用來和另一陣列或集合的值進行組合

```php
$collection = collect(['name', 'age']);

$combined = $collection->combine(['George', 29]);

$combined->all();

// ['name' => 'George', 'age' => 29]
```

##### `merge()` 合併指定的陣列或是集合到原集合

若傳入的集合中的 key 值與原集合中的 key 值相同，則傳入的值將會將原集合中的值覆蓋。

```php
$collection = collect(['product_id' => 1, 'price' => 100]);

$merged = $collection->merge(['price' => 200, 'discount' => false]);

$merged->all();

// ['product_id' => 1, 'price' => 200, 'discount' => false]
```

若傳入的集合項為數字，則這些值將會追加在集合的最後面。

```php
$collection = collect(['Desk', 'Chair']);

$merged = $collection->merge(['Bookcase', 'Door']);

$merged->all();

// ['Desk', 'Chair', 'Bookcase', 'Door']
```

##### `concat()` 將傳入的值追加到集合的末端

```php
$collection = collect(['John Doe']);

$concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

$concatenated->all();

// ['John Doe', 'Jane Doe', 'Johnny Doe']
```

##### `push()` 把指定的值加入集合的末端

```php
$collection = collect([1, 2, 3, 4]);
$collection->push(5);
$collection->all();

// [1, 2, 3, 4, 5]
```

##### `prepend()` 將指定的值加入集合的開頭

```php
$collection = collect([1, 2, 3, 4, 5]);

$collection->prepend(0);

$collection->all();

// [0, 1, 2, 3, 4, 5]
```

#### 擷取

##### `except()` 擷取除了 a, b 以外的

##### `only()` 只擷取 a, b

##### `get()` 取得特定 key 的值

##### `forget()` 直接刪除指定 key 值對應的 value

##### `pull()` 和 `get()` 雷同，會修改原本的 collection

##### `pluck()` 取得集合中所有陣列的 key 值

```php
$collection = collect([
    ['product_id' => 'prod-100', 'name' => 'Desk'],
    ['product_id' => 'prod-200', 'name' => 'Chair'],
])

$picked = $collection->pluck('name');

$pluck->all();

// ['Desk', 'Chair']
```

##### `intersect` 從原集合中移除在指定陣列中或集合中不存在的值

```php
$collection = collect(['a', 'b', 'c']);
$intersect = $collection->intersect(['a', 'c', 'e', 'f']);
$intersect->all(); // [0 => 'a', 2 => 'c']
```

##### `keys()`, `values()` 取出集合中的 key 或 value

#### 轉型

##### `toArray()`, `toJson()` 提供轉成陣列或是 json 等常用格式

### Where

collection 和 laravel 本身的 ORM 系統 Eloquent 密不可分，因此也配合支援許多 where 語法，使用方法類似 MySQL。

#### 'first' 返回陣列中指令條件的第一元素

```php
collect([1, 2, 3, 4, 5, 6])->first(function ($value, $key) {
    return $value > 2;
});

// 3
```

若 `first` 方法不傳入參數，則返回集合中第一元素。若集合為空，則返回 `null`

```php
collect([1, 2])->first();

// 1
```

### 軟體操作類

#### `collect()` 複製一個新的 collection，記憶體位置不衝突

## Helper 輔助函數

### `Arr::add()` 將數值加入陣列

```php
use Illuminate\Support\Arr;

$array = Arr::add(['name' => 'Desk'], 'price', 100);

// ['name' => 'Desk', 'price' => 100]

$array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

// ['name' => 'Desk', 'price' => 100]

```

### `after()` 返回傳入的字串的值後面所有的內容

如果傳入的值不存在，將返回整個字串

```php
use Illuminate\Support\Str;

$slice = Str::of('This is my name')->after('This is');

// ' my name'
```
