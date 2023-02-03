# PHP 命名空間

> 參考資料：
>
> [namespace 命名空間詳解](https://learnku.com/articles/15064/namespace-namespace-detailed-explanation)

## 什麼是命名空間

在 php 中，語法規則不允許`變數`、`類別名稱`、`const 常數`在一個請求中出現多個相同的命名，若有應用程序不得不出現多個同名的`變數`、`類別名稱`、`const 常數`，此時可以將其放到不同的空間進行請求。

這個不同的空間就稱作`命名空間 namespace`。空間可以作為一個`容器`、`儲存類別`、`函數`、`const 常數`的容器。

- 同名元素在同一空間：

  ```php
  function getInfo(){
    echo "hello";
  }

  function getInfo(){
    echo "hello";
  }
  getInfo();
  ```

  同名稱的兩個 function 在同一個空間存取會報錯，錯誤碼：

  ```log
  Fatal error: Cannot redeclare getInfo() (previously declared in...)
  ```

- 同名元素在不同空間

  ```php
  namespace Test1;

  function getInfo() {
    echo "hello from test 1";
  }
  getInfo();
  echo "<br>";

  namespace Test2;

  function getInfo() {
    echo "hello from test 2";
  }
  getInfo();
  ```

  相同命名的兩個 function 放到不同空間進行存取，最後輸出：

  ```log
  hello from test 1
  hello from test 2
  ```

## 使用命名空間

### 命名規則

- 透過 namespace 關鍵字宣告命名空間

  ```php
  namespace 空間名稱  // 空間名稱依照 php 正確的命名方式定義即可
  ```

  namespace 針對 function、類別名稱、const 常數三個部份起作用，並統稱為元素。

### 常數的宣告

- define ($name, $value);

  在類別外部宣告常數，和命名空間沒有關係。同名稱的常數只能 define 一次

  ```php
  namespace Test1;
  define ('IVAN', 'hello world');

  namespace Test2;
  define ('IVAN', 'hello world');

  // Notice: Constants IVAN already defined in...
  ```

- const NAME = $value;
  和空間命名有關係。const 可以在類別的內部宣告常數值(類別常數)，也可以在類別外部宣告(正常常數)。

  使用空間命名的時候，const 可以放到類別外面宣告。相同名稱的多個常數，可以分別定義報不同的命名空間裡面。

  ```php
  namespace Test1;
  const USER = "ivan_1";
  echo USER;
  echo "<br>";

  namespace Test2;
  const USER = "ivan_2";
  echo USER;
  ```

  ```log
  ivan_1
  ivan_2
  ```

### 命名空間中 const 和 define 的區別

`const` 針對命名空間產生影響，`define` 不發生影響。

`const` 可以宣告多個相同名稱的常數。

`define` 宣告的名稱具有唯一性。

## 間單的元素存取

```php
// 空間的名稱與具體父層目錄沒有直接關係
// 按照 php 正確的命名方式定義即可
namespace Test1;

function getInfo() {
  echo "test_1";
}

const USER = "ivan_1";

namespace Test2;

function getInfo(){
  echo "test_2";
}
const USER = "ivan_2";

// 存取元素
// 當元素沒有任何限制的時候，會存取"目前空間"的元素
// 目前空間：離此呼叫 function 最近的命名空間
getInfo(); // test_2
echo USER; // ivan_2

// 當元素指定存取的命名空間
// 存取其他命名空間的元素：\空間\元素;
\Test1\getInfo(); // test_1
echo \Test1\USER; // ivan_1
```

## 子級(多級)空間

命名空間可以讓我們存放許多元素(函數、類別、常數)，當元素較多時，為了方便管理，可以對元素進行分類儲存，將命名空間設定為多級空間。

多級空間的最後一級空間就稱為子級空間。

- 空間元素存取的三種型式：

  - 非限定名稱方式
  - 完全限定名稱方式
  - 限定名稱

- 多級命名空間使用

  ```php
  // 多級命名空間使用
  namespace AAA\aaa\test;

  class Shop
  {
    public $impression = 'beautiful';
  }

  namespace BBB\bbb\test;

  class Shop
  {
    public $impression = 'science and technology';
  }

  // 非限定方式存取元素
  // 預設存取當前的空間元素
  $obj = new Shop();
  echo $obj->impression; // science and technology

  // 完全限定名稱方式存取元素
  // 存取其他空間元素
  $obj_1 = new \AAA\aaa\test\Shop();
  echo $obj_1->impression; // beautiful
  ```

- 多級空間使用

  ```php
  namespace AAA\aaa\test\library;
  const USER = 'ivan_1';

  namespace BBB\bbb\test;
  const USER = "ivan_2";

  namespace AAA\aaa\test;
  const USER = "ivan_3";

  // 限定名稱存取元素
  // 此方法存取元素規則：目前空間 + 本身空間\元素

  echo library\USER; // 命名空間為 \AAA\aaa\test\library\USER;
  // ivan_1
  ```

- 非限定名稱方式
  `echo Shop::$impression;` 就近存取上面與其最近的空間內的 Shop() 元素。類似 php 引入文件 `include("common.php");` 使用相對路徑引入當前目錄下的 common.php 文件。
- 限定名稱
  `echo library\Shop::$impression;` 將當前空間和 library 空間聯合，獲得 Shop() 元素。類似 `include("Common/Conf/config.php");` 相對路徑。
- 完全限定名稱方式
  `echo BBB\Shop::$impression;` 存取 BBB 空間的 Shop 元素。類似 `include("D:/web/1121/Conf/common.php);` 使用絕對路徑引入文件。

## 引入機制

命名空間可宣告為多級空間，此一多級空間元素在其他的空間內部存取的時候，不可以需要透過`完全限定名稱`，此一方法不方便維護及開發。

為了降低程式碼的複雜度，可以在當前的空間將指定的空間引入，進而透過`限定名稱`的形式，使用其他空間的元素。

### 空間引入

- use 空間

  ```php
  // 引入機制：空間引入
  namespace AAA\aaa\test;

  const USER = 'ivan';
  const HOST = 'localhost:80';

  function getInfo() {
    echo 'test';
  }

  namespace BBB\bbb\library;

  const USER = 'cindy';
  const HOST = 'localhost:443';
  function getInfo() {
    echo 'test';
  }

  /**
   * 項目需要頻繁存取其他空間元素
   * 為了降低存取其他空間的複雜度，可以將頻繁存取的空間引入當前的空間
   * 進而透過"限定名稱"方式存取元素
   * 限定名稱：被引入間的最後一級空間的名稱
   */

  use AAA\aaa\test;

  echo test\USER; // ivan
  test\getInfo(); // test
  echo test\HOST; // localhost:80
  // 返回最近的空間元素
  echo USER; // cindy
  ```

- 類別元素引入：

`use 空間\空間\空間\類元素;`

空間引入可以解決完全限定名稱訪問元素的繁瑣性，但是還需要透過限定名稱方式訪問空間。

若引入空間的元素是 class，就可以直接將這個類別引入到當前空間，使用的時候也就可以透過非限定名稱的方式訪問。

程式碼相對較為簡潔。

- 類別元素引入

  ```php
  namespace AAA\aaa\test;

  class Shop
  {
    static $name = 'ivan';
  }

  namespace BBB\bbb\test;
  const USER = 'cindy';

  // 將 AAA\aaa\test\Shop 類別元素引入
  use AAA\aaa\test\Shop;

  // 透過非限定名稱在此命名空間中存取引入的類別中的元素
  echo Shop::$name; // ivan
  ```

- 類別元素在引入時的特殊狀況
  當引入類別的命名和當前空間的類別名稱相同時：

  ```php
  namespace AAA\aaa\test;
  class Shop
  {
    static $name = 'ivan';
  }

  namespace BBB\bbb\test;
  const USER = 'cindy';
  class Shop
  {
    static $name = 'jack';
  }

  // 將 AAA\aaa\test 類別元素直接引入
  use AAA\aaa\test\Shop;

  // 透過非限定名稱存取引入的類別
  echo Shop::$name; // error

  // Fatal error: Cannot use AAA\aaa\test\Shop as Shop because the name is already in use in...
  ```

  解決方法：使用別名

  `use 空間\元素 as 別名;`

  把其他空間的一個類別元素引入到當前空間，若當前空間也已有一個同名的類別元素，則引入元素與當前空間的元素就會產生衝突，為了避免衝突，可以給引入的空間元素取一個別名。

  引入的 Shop 與當前空間的 Shop 有衝突取別名：

  ```php
    namespace AAA\aaa\test;
  class Shop
  {
    static $name = 'ivan';
  }

  namespace BBB\bbb\test;
  const USER = 'cindy';
  class Shop
  {
    static $name = 'jack';
  }

  // 將 AAA\aaa\test 類別元素直接引入
  use AAA\aaa\test\Shop as IntShop;

  // 透過別名存取引入的類別
  echo IntShop::$name; // ivan
  echo Shop::$name; // jack
  ```

### 公共空間

一個 php 文件裡面沒有 namespace 關鍵字宣告，則該文件的元素都存在於公共空間。

存取公共空間的元素統一設為：`\元素`

### CommonSpace.php include 引入 CommonSpace1.php

- CommonSpace.php

  ```php
  namespace AAA;

  function f1() {
    echo "in good mood";
  }

  // 在公共空間的檔案會被引入，針對當前空間不發生影想

  include("CommonSpace1.php"); // 公共空間

  // 存取元素
  f1(); // in good mood 當前空間就是 AAA 空間
  echo \NAME; // 存取公共空間的元素

  // 本身有命名空間，引入的檔案是公共空間，本身的空間存取不到時，會到別的空間去尋找此元素
  ```

- CommonSpace1.php

  ```php
  const NAME = 'ivan';

  function f1() {
    echo "okay";
  }

  function f2() {
    echo 'all good';
  }
  ```

CommonSpace.php 有 namespace，CommonSpace1.php 沒有(CommonSpace1.php 處於公共空間)。被引入的檔案空間，此時被引入的文件 CommonSpace1.php 屬於公共空間，針對當前空間不發生影響。

- 透過非限定名稱呼叫一個元素(function、常數)
  - 首先取得本空間元素
  - 其次取得公共空間元素

若在 CommonSpace.php 中將 function fi() 註解，此時 f1() 呼叫的 function 為公共空間 function f1()，輸出：

```log
okay
ivan
```

將 CommonSpace.php 的 function f1() 取消註解，此時 f1() 呼叫的是 AAA 命名空間的 function f1()。

```log
in good mood
ivan
```

### ReverseCommonSpace.php include 引入 ReverseCommonSpace １.php

- ReverseCommonSpace.php

  ```php
  function f1() {
    echo "in good mood";
  }

  const NAME = "cindy";

  function f2() {
    echo "good";
  }

  include("ReverseCommonSpace1.php") // 有命名空間

  \f2(); // good 存取公共空間需要有"反斜線"，提高程式碼可讀性
  echo NAME;
  echo \AAA\NAME;
  // f3(); // 無法存取會報錯，正確的存取寫法為： \AAA\f3();
  \AAA\f3();

  // 本身是公共空間，引入的檔案是有命名空間的，本身的空間無法存取時，不會到別的空間去找尋元素。
  ```

  若 f3(); 沒有註解掉會報錯：

  ```log
  good
  cindy
  ivan

  (!) Fatal error: Uncaught Error: Call to undefined function f3() in...
  ```

  將其註解後輸出：

  ```log
  good
  cindy
  ivan
  buy book
  ```

- ReverseCommonSpace1.php

  ```php
  namespace AAA;

  const NAME = 'ivan';

  function f2() {
    echo 'good';
  }

  function f3(){
    echo "buy book";
  }
  ```

## 範例與總結

### 錯誤範例

```php
const USER = 'ivan';

namespace AAA;

function getInfo() {
  echo 'OK';
}

// Fatal error: Namespace declaration statement has to be the very first statement or after any declare call in the script in...
```

正確做法：

```php
namespace AAA;

const USER = 'ivan';

function getInfo() {
  echo 'OK';
}

getInfo(); // OK
```

不能宣告常數在公共空間，而 function 在命名空間。

宣告命名空間時，在 namespace 關鍵字前面不能有任何程式碼，包刮 header 也要寫在下面。

### 命名空間總結

1. 宣告命名空間時，在 namespace 關鍵字前面不能有任何程式碼(可以註解)，包刮 header 也要寫在下面。
2. 命名空間是虛擬抽象的空間，非真實的檔案路徑。
3. 同一請求多檔案可以使用相同的命名空間，只要這些檔案中不會出現多的同名稱、同類型的元素(function, const)即可。
