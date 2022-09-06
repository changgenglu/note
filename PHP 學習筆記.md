# PHP 學習筆記

###### tags: `php`

- [PHP 學習筆記](#php-學習筆記) - [tags: `php`](#tags-php)
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
    - [`sort` 陣列排序](#sort-陣列排序)
      - [依 value 排序](#依-value-排序)
      - [依 key 排序](#依-key-排序)
      - [自訂排序](#自訂排序)
    - [分割字串](#分割字串)
      - [`explode( string $delimiter , string $string , int $limit )`](#explode-string-delimiter--string-string--int-limit-)
      - [`str_split(string,length)`](#str_splitstringlength)
      - [`array_slice(array,start,length,preserve)`](#array_slicearraystartlengthpreserve)
    - [`implode(separator,array)` 將陣列轉為字串](#implodeseparatorarray-將陣列轉為字串)
    - [`array_filter($arrayName, $callbackFunction, $callbackParameter)` 過濾陣列元素](#array_filterarrayname-callbackfunction-callbackparameter-過濾陣列元素)

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
foreach ($array as $value) {
  // 每次尋訪會將陣列的值存到value中，直到陣列結束
}
foreach ($array as $key => $value) {
	// 每次尋訪會將陣列的值以及key，存到value中  key => 流水號
}
```

- `array_key_first()` 取得陣列中第一個 key 值
- `array_key_last()` 取得陣列中最後一個 key 值

```php
$array  = array("dog", "rabbit", "horse", "rat", "cat");
foreach($array as $index => $animal) {
    if ($index === array_key_first($array))
        echo $animal; // output: dog

    if ($index === array_key_last($array))
        echo $animal; // output: cat
}
```

- `break` 跳出迴圈

```php
<?php
foreach (array('1','2','3') as $first) {
    echo "$first ";
    foreach (array('3','2','1') as $second) {
        echo "$second ";
        if ($first == $second) {
            break;  // this will break both foreach loops
        }
    }
    echo ". ";  // never reached!
}
echo "Loop Ended";
?>
```

輸出

```log
1 3 2 1 . 2 3 2 . 3 3 . Loop Ended
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

```log
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

- 進階用法

  藉由其查詢兩個以上陣列之間的差異，並返回不存在陣列中的值之特性。

  因此可用來刪除陣列中多個值，而不影響其索引值。

  ```php
  <?php
  //Declare the array
  $flowers = [
    "Rose",
    "Lili",
    "Jasmine",
    "Hibiscus",
    "Tulip",
    "Sun Flower",
    "Daffodil",
    "Daisy"
  ];

  $flowers = array_diff($flowers, array("Rose","Lili"));
  echo "The array is:\n";
  print_r($flowers);
  ```

  輸出

  ```log

  Array
  (
  [2] => Jasmine
  [3] => Hibiscus
  [4] => Tulip
  [5] => Sun Flower
  [6] => Daffodil
  [7] => Daisy
  )
  ```

### `sort` 陣列排序

#### 依 value 排序

- 由小到大排序值
- `sort` 刪除 key
- `asort` 保留 key
- 由大到小排序值
- `rsort` 刪除 key
- `arsort` 保留 key

#### 依 key 排序

- `ksort` 由小到大排索引值
- `krsort` 由大到小排索引值

#### 自訂排序

加上一個前綴 `u` 在相對應的方法

- 範例一：
  今天有一個陣列如下

```php
$unsorted = [
    ['name'   => 'good',
     'sorter' => '1',],

    ['name'   => 'bad',
     'sorter' => '3',],

    ['name'   => 'normal',
     'sorter' => '2',],
];
```

我要透過 sorter 這個 key 的 value 來做排序

```php
usort($unsorted, function ($a, $b) {
    return $a['sorter'] > $b['sorter'];
    // 如果 a > b 的話 就會輸出 1，而因為 usort 的 根基是 sort
    // 意即是照 value 由小到大排序，所以輸出 1 的就會往後排，進而達到目的
});
```

output

```log
array(3) {
 [0]=>
 array(2) {
   ["name"]=>
   string(4) "good"
   ["sorter"]=>
   string(1) "1"
 }
 [1]=>
 array(2) {
   ["name"]=>
   string(6) "normal"
   ["sorter"]=>
   string(1) "2"
 }
 [2]=>
 array(2) {
   ["name"]=>
   string(3) "bad"
   ["sorter"]=>
   string(1) "3"
 }
}
```

- 範例二：

  如果一樣的陣列，但要用來比對的數值是重複的

  ```php
  $unsorted = [
      ['name'   => 'good',
       'sorter' => '1',],

      ['name'   => 'bad',
       'sorter' => '3',],

      ['name'   => 'normal',
       'sorter' => '3',],
  ];
  ```

  可以增加一個比對條件

  ```php
  $unsorted = [
      ['name'   => 'good',
       'sorter' => '1',
      'newSorter'=> '2'],

      ['name'   => 'bad',
       'sorter' => '3',
      'newSorter'=> '3'],

      ['name'   => 'normal',
       'sorter' => '3',
      'newSorter' => '1'],

      ['name'   => 'hahaha',
       'sorter' => '2',
       'newSorter' => '1'],
  ];
  ```

  依照 sorter 來進行排序，但如果 sorter 數值相同，則使用 newSorter 來進行排序

  ```php
  usort($unsorted, function ($a, $b)) {
      return $a['sorter'] > $b['sorter'] || ($a['sorter'] == $b['sorter'] && $a['newSorter'] > $b['newSorter']);
  }

  // 或這樣寫

  if ($a['sorter'] > $b['sorter'] || ($a['sorter'] == $b['sorter'] && $a['newSorter'] > $b['newSorter'])) {
      return 1;
  } elseif ($a['sorter'] < $b['sorter']) {
      return -1;
  } else {
      return 0;
  }
  ```

  output

  ```log
  array(4) {
    [0]=>
    array(3) {
      ["name"]=>
      string(4) "good"
      ["sorter"]=>
      string(1) "1"
      ["newSorter"]=>
      string(1) "2"
    }
    [1]=>
    array(3) {
      ["name"]=>
      string(6) "hahaha"
      ["sorter"]=>
      string(1) "2"
      ["newSorter"]=>
      string(1) "1"
    }
    [2]=>
    array(3) {
      ["name"]=>
      string(6) "normal"
      ["sorter"]=>
      string(1) "3"
      ["newSorter"]=>
      string(1) "1"
    }
    [3]=>
    array(3) {
      ["name"]=>
      string(3) "bad"
      ["sorter"]=>
      string(1) "3"
      ["newSorter"]=>
      string(1) "3"
    }
  }
  ```

### 分割字串

#### `explode( string $delimiter , string $string , int $limit )`

- string $delimiter - 字串的切割部位，請自行設定，字串形態，必填
- string $string - 被要處理的字串，字串形態，必填項目。
- int $limit - 設定字串切割後最多可輸出的數量，數字形態，可為正整數或負整數，如果填寫正整數，最後的的部份包含切割完剩下的所有部份，，如果填寫負整數，則倒數的部份若在負整數範圍內將不會顯示，非必填項目

```php
<?php
  $str = 'Apple Dog Pig';
  $str_sec = explode(" ",$str);
  print_r($str_sec);
```

輸出

```log
Array (
　[0] => Apple
　[1] => Dog
　[2] => Pig
)
```

加入`$limit` 參數

```php
<?php
  $str = 'Apple Dog Pig';
  $str_sec_A = explode(" ",$str,2);
  $str_sec_B = explode(" ",$str,-1);
  print_r($str_sec_A);
  print_r($str_sec_B);
```

```log
Array (
　[0] => Apple
　[1] => Dog Pig
)
Array (
　[0] => Apple
　[1] => Dog
)
```

#### `str_split(string,length)`

- `string` 必需。規定要分割的字符串。
- `length` 可選。規定每個數組元素的長度。默認是 1。

```php
<?php
  $NewString = "M'L2";
  $Arr2=str_split($NewString,3);//根據每三個字元切割
  print_r($Arr2);
```

輸出

```log
Array
(
    [0] => M'L
    [1] => 2
)
```

#### `array_slice(array,start,length,preserve)`

- `array` 必填，傳入陣列。
- `start` 必填，規定取出元素的開始位置，0 = 第一個元素，若傳入正數，則由前往後取值，若為負值由後往前取值。
- `length` 選填，規定返回的陣列長度。
- `preserve` 選填，`true` 保留 key 值，`false` 重置 key 值。

```php
<?php
$a=array("red","green","blue","yellow","brown");
print_r(array_slice($a,2));
```

```log
Array
(
    [0] => blue
    [1] => yellow
    [2] => brown
)
```

### `implode(separator,array)` 將陣列轉為字串

- `separator` 可選。規定數組元素之間放置的內容。默認是 ""（空字符串）。
- `array` 必需。要結合為字符串的數組。

```php
<?php
$arr = [1,2,3,4,5,6];
print_r(implode('=', $arr));
```

```log
"1=2=3=4=5=6"
```

### `array_filter($arrayName, $callbackFunction, $callbackParameter)` 過濾陣列元素

- `$arrayName` 必須，目標陣列
- `$callbackFunction` 可選，指定刪除的參數，預設刪除陣列中等於 false 的值
- `$flag` 可選，引用傳遞給回傳函數的參數
  - `ARRAY_FILTER_USE_KEY` 將 key 作為唯一參數傳遞給回調函數，而不是數組的值
  - `ARRAY_FILTER_USE_BOTH` 將值和鍵都作為參數而不是值傳遞給回調

```PHP
<?php

// PHP function to check for even elements in an array
function Even($array)
{
    // returns if the input integer is even
    if($array%2==0)
       return TRUE;
    else
       return FALSE;
}

$array = array(12, 0, 0, 18, 27, 0, 46);
print_r(array_filter($array, "Even"));

```

輸出

```log
Array
(
    [0] => 12
    [1] => 0
    [2] => 0
    [3] => 18
    [5] => 0
    [6] => 46
)
```

### `str_pad(string,length,pad_string,pad_type)` 填充字串為指定長度

- string 必填，要填充的字串。
- length 必填，規定新字串的長度，若小於傳入的字串長度，則不進行操作。
- pad_string 可選，提供填充的字串，預設為空白。
- pad_type 可選，字串填充的方向。
  - STR_PAD_BOTH 填充字串的兩側，若不為偶數，則將額外的字串填充至右側。
  - STR_PAD_LEFT 填充到字串的左側。
  - STR_PAD_RIGHT 填充到字串的右側(預設)。

```php
<?php
$str = "Hello world";
echo str_pad($str, 20, ".", STR_PAD_LEFT);
```

輸出

```log
.........Hello World
```

```php
<?php
$str = "Hello world";
echo str_pad($str, 20, ".:", STR_PAD_BOTH);
```

輸出

```php
.:.:Hello World.:.:.
```

### 資料序列化及反序列化

#### `string serialize ( mixed $value )` 序列化

- $value: 要序列化的對象或陣列

```php
<?php
$sites = array('Google', 'Runoob', 'Facebook');
$serialized_data = serialize($sites);
echo  $serialized_data . PHP_EOL;
```

輸出

```log
a:3:{i:0;s:6:"Google";i:1;s:6:"Runoob";i:2;s:8:"Facebook";}
```

#### `mixed unserialize ( string $str )` 反序列化

- $str: 序列化後的字串

```php
$str = 'a:3:{i:0;s:6:"Google";i:1;s:6:"Runoob";i:2;s:8:"Facebook";}';
$unserialized_data = unserialize($str);
print_r($unserialized_data);
```

輸出

```log
Array
(
    [0] => Google
    [1] => Runoob
    [2] => Facebook
)
```

### `file_get_contents(path,include_path,context,start,max_length)`

將本地文件存入一個變數中

- path (必須) 文件的路徑
- include_path (可選) 如果也想在 include_path 中搜尋文件，可以將該參數設為"1"
- context (可選) 規定文件控制代碼的環境
- start (可選) 指定在文件中開始讀取的位置。
- max_length (可選) 規定讀取的位元組。

### `str_pad ($str, $pad_length , $pad_string, $pad_type)` 補足字串

- `$str` 來源字串
- `$pad_length` 補完後字串長度
- `$pad_string` 補入的字元
- `$pad_type` 補入的規則
  - `STR_PAD_BOTH` 左右都補
  - `STR_PAD_LEFT` 從左邊開始
  - `STR_PAD_RIGHT` 從右邊開始

範例：把 id 由左邊開始補 0，補到五位數

```php
$id=01;
$id=str_pad($id,5,"0",STR_PAD_LEFT);
echo $id;
//00001
```

### 將字串轉換為數值

> 若字串開頭為 0，轉為數值後開頭的 0 會被省略

- `number_format()` 若失敗則返回`E_WARNING`
- 使用類型轉換

  ```php
  $num = "1000.314";
  echo (int)$num
  ```

- 透過運算子將字串轉為數值，例如在字串中 + 0
