# Laravel Collections & Helper

- [Laravel Collections \& Helper](#laravel-collections--helper)
  - [Collections 集合](#collections-集合)
    - [`pluck()` 取得集合中所有陣列的 key 值](#pluck-取得集合中所有陣列的-key-值)
    - [`diff()` 比較集合或陣列的值](#diff-比較集合或陣列的值)
    - [`filter()` 傳入匿名函式篩選集合](#filter-傳入匿名函式篩選集合)
    - [`concat()` 將傳入的值追加到集合的末端](#concat-將傳入的值追加到集合的末端)
    - [`push()` 把指定的值加入集合的末端](#push-把指定的值加入集合的末端)
    - [`prepend()` 將指定的值加入集合的開頭](#prepend-將指定的值加入集合的開頭)
    - [`contains()` 判斷集合是否包含指定的條件](#contains-判斷集合是否包含指定的條件)
    - [`map()` 遍歷集合](#map-遍歷集合)
    - [`count()` 計數](#count-計數)
    - [`countBy()` 計算指定數值](#countby-計算指定數值)
  - [Helper 輔助函數](#helper-輔助函數)
    - [`Arr::add()` 將數值加入陣列](#arradd-將數值加入陣列)
    - [`Arr::sort()` 將陣列重新排列](#arrsort-將陣列重新排列)
    - [`after()` 返回傳入的字串的值後面所有的內容](#after-返回傳入的字串的值後面所有的內容)

## Collections 集合

### `pluck()` 取得集合中所有陣列的 key 值

```php
$collection = collect([
    ['product_id' => 'prod-100', 'name' => 'Desk'],
    ['product_id' => 'prod-200', 'name' => 'Chair'],
])

$picked = $collection->pluck('name');

$pluck->all();

// ['Desk', 'Chair']
```

### `diff()` 比較集合或陣列的值

返回不存在此方法參數中的值

```php
$diff = collect([1, 2, 3, 4, 5])->diff([2, 4, 6, 8]);
$diff->all();

// 返回[1, 3, 5]，原集合內與diff方法中相同的數值被剔除。
```

### `filter()` 傳入匿名函式篩選集合

返回通過篩選的項目

```php
$filtered = collect([1, 2, 3, 4])->filter(function (item) {
    return $item > 2
});

$filtered->all();

// [3, 4]
```

### `concat()` 將傳入的值追加到集合的末端

```php
$collection = collect(['John Doe']);

$concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

$concatenated->all();

// ['John Doe', 'Jane Doe', 'Johnny Doe']
```

### `push()` 把指定的值加入集合的末端

```php
$collection = collect([1, 2, 3, 4]);
$collection->push(5);
$collection->all();

// [1, 2, 3, 4, 5]
```

### `prepend()` 將指定的值加入集合的開頭

```php
$collection = collect([1, 2, 3, 4, 5]);

$collection->prepend(0);

$collection->all();

// [0, 1, 2, 3, 4, 5]
```

### `contains()` 判斷集合是否包含指定的條件

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

### `map()` 遍歷集合

將集合的值透過傳入的匿名函數修改並返回，生成修改過的新集合

```php
$collection = collect([1, 2, 3, 4, 5]);

$multiplied = $collection->map(function ($item, $key) {
    return $item * 2;
});

$multiplied->all();

// [2, 4, 6, 8, 10]
```

### `count()` 計數

```php
$collection = collect([1, 2, 3, 4]);

$collection->count();

// 4
```

### `countBy()` 計算指定數值

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

## Helper 輔助函數

### `Arr::add()` 將數值加入陣列

```php
use Illuminate\Support\Arr;

$array = Arr::add(['name' => 'Desk'], 'price', 100);

// ['name' => 'Desk', 'price' => 100]

$array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

// ['name' => 'Desk', 'price' => 100]

```

### `Arr::sort()` 將陣列重新排列

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

### `after()` 返回傳入的字串的值後面所有的內容

如果傳入的值不存在，將返回整個字串

```php
use Illuminate\Support\Str;

$slice = Str::of('This is my name')->after('This is');

// ' my name'
```
