# Laravel Collections & Helper

###### tags: `php` `Laravel`

- [Laravel Collections & Helper](#laravel-collections--helper) - [tags: `php` `Laravel`](#tags-php-laravel)
  - [Collections 集合](#collections-集合)
  - [Helper 輔助函數](#helper-輔助函數)

## Collections 集合

- `pluck()` 取得集合中所有陣列的 key 值，括號中為 key 值。

  ```php=
  $collection = collectc([
      ['product_id' => 'prod-100', 'name' => 'Desk'],
      ['product_id' => 'prod-200', 'name' => 'Chair'],
  ])

  $pucked = $colection->pluck('name');

  $pluck->all();

  // ['Desk', 'Chair']
  ```

- `contains()`用來判斷該集合是否含有指定的項目，並返回布林。

- `diff()` 此方法會比較其他集合或是純 php 陣列裡的值，並返回沒有在此方法參數中的值。diff(差異)。

  ```php=
  $diff = collect([1, 2, 3, 4, 5])->diff([2, 4, 6, 8]);
  $diff->all();

  // 返回[1, 3, 5]，原集合內與diff方法中相同的數值被剔除。
  ```

- `filter()`用傳入的匿名函式篩選集合，只回傳通過篩選的項目。

  ```php=
  $filtered = collect([1, 2, 3, 4])->filter(function (item) {
      return $item > 2
  });

  $filtered->all();

  // [3, 4]
  ```

- `concat()` 此方法會將傳入的陣列或集合，追加到集合的末端。

  ```php=
  $collection = collect(['John Doe']);

  $concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

  $concatenated->all();

  // ['John Doe', 'Jane Doe', 'Johnny Doe']
  ```

- `contains()` 此方法會判斷集合是否包含指定的條件

  傳入值

  ```php=
  $collection = collect(['name' => 'Desk', 'price' => 100]);

  $collection->contains('Desk');

  // true

  $collection->contains('New York');

  // false
  ```

  傳入陣列

  ```php=
  $collection = collect([
      ['product' => 'Desk', 'price' => 200],
      ['product' => 'Chair', 'price' => 100],
  ]);

  $collection->contains('product', 'Bookcase');

  // false
  ```

  傳遞匿名函數

  ```php=
  $collection = collect([1, 2, 3, 4, 5]);

  $collection->contains(function ($value, $key) {
      return $value > 5;
  });

  // false
  ```

- `containsStrict()` 方法和`contains()` 類似，但他在比較時更嚴格。

- `map()` 此方法遍歷集合，將集合的值透過傳入的匿名函數修改並返回，生成修改過的新集合。

  ```php=
  $collection = collect([1, 2, 3, 4, 5]);

  $multiplied = $collection->map(function ($item, $key) {
      return $item * 2;
  });

  $multiplied->all();

  // [2, 4, 6, 8, 10]
  ```

## Helper 輔助函數

- `after()` 返回傳入的字串的值後面所有的內容，如果傳入的值不存在，他將返回整個字串

  ```php
  use Illuminate\Support\Str;

  $slice = Str::of('This is my name')->after('This is');

  // ' my name'
  ```
