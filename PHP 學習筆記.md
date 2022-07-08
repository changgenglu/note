# PHP 學習筆記

###### tags: `php`

- [PHP 學習筆記](#php-學習筆記)
          - [tags: `php`](#tags-php)
  - [運算子、判斷](#運算子判斷)
    - [`+`: 算術相加](#-算術相加)
    - [`.`: 字串相加](#-字串相加)
    - [`gettype()`: 判斷變數的型態](#gettype-判斷變數的型態)
    - [`(int)($var1 + $var2)`: 只取商](#intvar1--var2-只取商)
    - [`isset($var)`: 檢查變數是否有設置](#issetvar-檢查變數是否有設置)
    - [`empty($var)`: 檢查變數是否為空值](#emptyvar-檢查變數是否為空值)
    - [`is_null($var)`: 檢查變數是否為 null](#is_nullvar-檢查變數是否為-null)
    - [`var_dump($var);`: 將變數的訊息印出於螢幕上](#var_dumpvar-將變數的訊息印出於螢幕上)
  - [方法](#方法)
    - [`foreach()` 尋訪陣列](#foreach-尋訪陣列)
    - [`scandir()` 掃描指定的目錄，並返回為陣列](#scandir-掃描指定的目錄並返回為陣列)
    - [`list(var1, var2...)` 宣告陣列中的值，使其成為變數](#listvar1-var2-宣告陣列中的值使其成為變數)
    - [`append(var1, var2)` 將傳入的值附加進陣列](#appendvar1-var2-將傳入的值附加進陣列)
    - [`array_fill()` 將值加入到陣列](#array_fill-將值加入到陣列)
    - [`array_combine()` 將傳入的參數合併為陣列](#array_combine-將傳入的參數合併為陣列)
    - [`array_unique($array, $flags)` 從陣列中刪除重複的值](#array_uniquearray-flags-從陣列中刪除重複的值)
    - [`array_diff()` 判斷陣列之間差異](#array_diff-判斷陣列之間差異)

## 運算子、判斷

### `+`: 算術相加

### `.`: 字串相加

### `gettype()`: 判斷變數的型態

### `(int)($var1 + $var2)`: 只取商

### `isset($var)`: 檢查變數是否有設置

### `empty($var)`: 檢查變數是否為空值

### `is_null($var)`: 檢查變數是否為 null

|                 | gettype() |   isset()   |   empty()   |  is_null()  |
| :-------------: | :-------: | :---------: | :---------: | :---------: |
| $x is undefined |   null    | **_false_** |  [true](#)  |  [true](#)  |
|    $x = null    |   null    | **_false_** |  [true](#)  |  [true](#)  |
|     $x = 0      |    int    |  [true](#)  |  [true](#)  | **_false_** |
|    $x = "0"     |    str    |  [true](#)  |  [true](#)  | **_false_** |
|     $x = 1      |    int    |  [true](#)  | **_false_** | **_false_** |
|     $x = ""     |    str    |  [true](#)  |  [true](#)  | **_false_** |
|   $x = "PHP"    |    str    |  [true](#)  | **_false_** | **_false_** |

### `var_dump($var);`: 將變數的訊息印出於螢幕上

## 方法

### `foreach()` 尋訪陣列

```php
foreach ($陣列 as $value) {
  // 每次尋訪會將陣列的值存到value中，直到陣列結束
}
foreach ($陣列 as $key => $value) {
	// 每次尋訪會將陣列的值以及key，存到value中  key => 流水號
}
```

### `scandir()` 掃描指定的目錄，並返回為陣列

### `list(var1, var2...)` 宣告陣列中的值，使其成為變數

```php
$my_array = array('dog', 'cat', 'horse');

list($a, $b, $c) = $my_array;
echo "i have serveral animals, a $a, a $b, a $c. ";

// i have several animals, a dog, a cat and a horse.
```

### `append(var1, var2)` 將傳入的值附加進陣列

```php
// PHP function to illustrate the
// append() method

$arrObj = new ArrayObject(array('Geeks',
                        'for', 'Geeks'));

// Appending an array
$arrObj->append(array('welcomes', 'you'));

var_dump($arrObj);
```

輸出

```PHP
object(ArrayObject)#1 (1) {
  ["storage":"ArrayObject":private]=>
  array(4) {
    [0]=>
    string(5) "Geeks"
    [1]=>
    string(3) "for"
    [2]=>
    string(5) "Geeks"
    [3]=>
    array(2) {
      [0]=>
      string(8) "welcomes"
      [1]=>
      string(3) "you"
    }
  }
}
```

### `array_fill()` 將值加入到陣列

`array_fill(int $start_index, int $count, mixed $value): array` 將傳入的`$value`，加入`$count` 個值到陣列，開始的 key 值由`$start_index` 指定

**$start_index**: 回傳陣列的第一個 key 值，如為負數，返回的第一個 key 將會是 start_index 的值，而後面的 key 值由 0 開始。

**$count**: 插入值的數量，需大於等於 0 ，否則拋出 E_WARNING。

**$value**: 傳入陣列的值。

```php
<?php
$a = array_fill(5, 6, 'banana');
$b = array_fill(-2, 4, 'pear');
print_r($a);
print_r($b);
?>
```

回傳

```PHP
Array
(
  [5]  => banana
  [6]  => banana
  [7]  => banana
  [8]  => banana
  [9]  => banana
  [10] => banana
)
Array
(
  [-2] => pear
  [0] => pear
  [1] => pear
  [2] => pear
)
```

### `array_combine()` 將傳入的參數合併為陣列

`array_combine(array $keys, array $values): array` `$key`為 key 值，`$value` 為相對應的值。

```php
$a = array('green', 'red', 'yellow');
$b = array('avocado', 'apple', 'banana');
$c = array_combine($a, $b);

print_r($c);
```

輸出

```PHP
Array
(
  [green]  => avocado
  [red]    => apple
  [yellow] => banana
)
```

### `array_unique($array, $flags)` 從陣列中刪除重複的值

- `$array` 要刪除重複值的陣列
- `$fiags` 指定陣列的排序模式，有五種型別
  - `SORT_REGULAR` 正常常比較元素
  - `SORT_NUMERIC` 以數字方式比較元素
  - `SORT_STRING` 以字串方式比較元素
  - `SORT_LOCALE_STRING` 基於當前的語言環境，以字串方式比較元素。

### `array_diff()` 判斷陣列之間差異

`array_diff( $array1 , $array2 , $array3 , ... ):array` 後面每個陣列都跟第一個陣列做比較，此方法會回傳在第一陣列中有出現，但未出現在其他陣中的值，並會保留鍵名

```php
<?php
$array1 = array('A','B','C','D');
$array2 = array('C','D','E','F');
$array3 = array('A','B','E','F');
$newArray1 = array_diff($array1,$array2);
print_r($newArray1);
$newArray2 = array_diff($array1,$array3);
print_r($newArray2);

```

輸出

```PHP
Array
(
  [0] => A,
  [1] => B
)
Array
(
  [2] => C,
  [3] => D
)
```
