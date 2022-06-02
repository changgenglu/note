# PHP 學習筆記

###### tags: `php`

`+` => 算術相加

`.` => 字串相加

`gettype()` => 判斷變數的型態

`(int)($var1 + $var2);` => 只取商

`isset($var);` => 檢查變數是否有設置

`empty($var);` => 檢查變數是否為空值

`is_null($var);` => 檢查變數是否為 null

|                 | gettype() |  isset()  |  empty()  | is_null() |
| :-------------: | :-------: | :-------: | :-------: | :-------: |
| $x is undefined |   null    | **false** |  _true_   |  _true_   |
|    $x = null    |   null    | **false** |  _true_   |  _true_   |
|     $x = 0      |    int    |  _true_   |  _true_   | **false** |
|    $x = "0"     |    str    |  _true_   |  _true_   | **false** |
|     $x = 1      |    int    |  _true_   | **false** | **false** |
|     $x = ""     |    str    |  _true_   |  _true_   | **false** |
|   $x = "PHP"    |    str    |  _true_   | **false** | **false** |

`var_dump($var);` => 將變數的訊息印出於螢幕上

foreach 尋訪陣列

```php
foreach ($陣列 as $value) {
    // 每次尋訪會將陣列的值存到value中，直到陣列結束
}
foreach ($陣列 as $key => $value) {
	// 每次尋訪會將陣列的值以及key，存到value中  key => 流水號
}
```

`scandir()` 掃描指定的目錄，並返回為陣列。

`list(var1, var2...)` 宣告陣列中的值，使其成為變數

```php=
$my_array = array('dog', 'cat', 'horse');

list($a, $b, $c) = $my_array;
echo "i have serveral animals, a $a, a $b, a $c. ";

// i have several animals, a dog, a cat and a horse.
```
