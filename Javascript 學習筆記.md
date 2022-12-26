# Javascript 學習筆記

- [Javascript 學習筆記](#javascript-學習筆記)
  - [基本概念](#基本概念)
    - [宣告與命名](#宣告與命名)
  - [運算式與運算子](#運算式與運算子)
    - [嚴謹模式](#嚴謹模式)
    - [賦值運算子](#賦值運算子)
    - [比較運算子](#比較運算子)
    - [算數運算子](#算數運算子)
    - [邏輯運算子](#邏輯運算子)
    - [其餘運算子與展開運算子 `...`](#其餘運算子與展開運算子-)
    - [三元運算式](#三元運算式)
    - [if else](#if-else)
  - [流程判斷與迴圈](#流程判斷與迴圈)
    - [switch](#switch)
    - [while 迴圈](#while-迴圈)
    - [for 迴圈](#for-迴圈)
    - [for of](#for-of)
    - [for in](#for-in)
  - [函式 function](#函式-function)
    - [定義函式](#定義函式)
    - [箭頭函式](#箭頭函式)
    - [變數的有效範圍 (Scope)](#變數的有效範圍-scope)
    - [提升(Hoisting)](#提升hoisting)
    - [全域變數](#全域變數)
  - [方法](#方法)
    - [取得 base\_url](#取得-base_url)
    - [document](#document)
      - [`.querySelector()` 元素選擇器](#queryselector-元素選擇器)
      - [`.querySelectorAll()` 選取所有指定元素](#queryselectorall-選取所有指定元素)
    - [prototype.forEach()](#prototypeforeach)
    - [prototype.map()](#prototypemap)
    - [prototype.push()](#prototypepush)
    - [`Math.round()` 四捨五入](#mathround-四捨五入)
    - [`Array.prototype.filter()`](#arrayprototypefilter)
    - [`Array.prototype.splice()` 新增刪除陣列中的元素](#arrayprototypesplice-新增刪除陣列中的元素)
    - [物件取值、新增與刪除](#物件取值新增與刪除)
      - [物件取值](#物件取值)
      - [物件轉為陣列](#物件轉為陣列)
      - [新增物件屬性](#新增物件屬性)
      - [刪除物件屬性](#刪除物件屬性)
    - [SET 集合](#set-集合)
      - [基本使用](#基本使用)
      - [陣列與集合間轉換](#陣列與集合間轉換)
      - [過濾陣列中重複的元素](#過濾陣列中重複的元素)
    - [JSON 轉換](#json-轉換)
      - [`JSON.stringify()` 將物件轉為 json 字串](#jsonstringify-將物件轉為-json-字串)
      - [`JSON.parse()` 將 json 字串轉換為物件](#jsonparse-將-json-字串轉換為物件)
  - [額外補充](#額外補充)
    - [randome(亂數)公式](#randome亂數公式)

> **參考資料：**
>
> [重新認識 javascript](https://ithelp.ithome.com.tw/users/20065504/ironman/1259)

## 基本概念

- Javascript 的原始值(基本型別/primitives)：

```javascript
12345; // int
("12345"); // string
true, false; // bool
null; // empty
undefined; // 未定義
```

- Javascript 的複合值(物件型別 => object)：包含一個或多個原始值，像是物件或是物件實字
- Javascript 物件建構式：

```javascript
Nunmber()、String()、Boolean()、
Object()、Array()、Function()、
Date()、RegExp()、Error()
```

- 物件：使用 new 關鍵字建立物件

```javascript
const name = new type(arguments);
const d = new Date();
```

- 物件實字

```javascript
var obj = {
  name: "eason",
  action: "haha",
};
```

- 陣列

```javascript
var arr = [1, 2, 3, 4, 5];
arr[8] = 12;
arr = [1, 2, 3, 4, 5, "", "", "", 12];
```

- 用 new 建構出來的是物件 object

```javascript
    var a = new String("test");
    typeof(a) = object
```

- 只有建構式，則會轉為原始值

```javascript
    var a = String("test");
    typeof(a) = string
```

- 複合值在 javascript 是透過記憶體中的位址來比對

```javascript
var a = new String("test");
var b = new String("test");

console.log(a === b);
// false
```

- 將不同型態的物件通通轉為字串  
  \`${}\` 在大括號中加入變數

- 宣告原始值：單獨放一個記憶體位址 以 by value 運作
- 宣告複合值：包含許多原始值，但是只放在一個記憶體位置 以 by reference(參考) 運作

- 更改變數為參考物件(複合值)內的原始值，記憶體位址不變
- 更改變數為原始值，會更改變數的記憶體位址

- undefined  
  這地方沒有這個東西，所以你無法使用
- NaN  
   要轉型成數字時傳入參數非數字的時候
- null  
  這地方會有一個值，但這個值目前還沒準備好的意思，所以先填入 `null`

- this
  - 物件掛在誰身上就是`this`，`this`只在當下單一層的作用域裡有效果，箭頭函式就不會。
  - 如果宣告變數，則在宣告當層以及內層為有效範圍。
  - 單純的呼叫`this`，`this`會變成 Global

.bind //定義 function 內的 this 是什麼

### 宣告與命名

在 ES5 之前都只會用 `var` 宣告變數，在 ES6 之後加入 `let` 和 `const`，現在應以新加入的特性進行宣告。

`const`：常數，宣告之後無法再次指定值，只讀不能寫。
`let`：變數，暫存的資料，可以更動裡面的值。

- 命名規則
  - 開頭字元需要是 ASCII 字元(英文小寫)，或是下底線(\_)、錢號($)。開頭字元不得使用數字。
  - 大小寫敏感
  - 名稱不得使用保留字

**注意** 下底線開頭的命名常為特別用途：如類別中的私有變數、常數或方法。錢符號也通常為特殊用途命名。

變數與方法名稱都用小駝峰式的命名，類別用大駝峰式命名。

## 運算式與運算子

### 嚴謹模式

- 宣告在主程式開頭：Global Scope，所有的程式都會在嚴謹模式下執行。
- 宣告在函數開頭：Function Scope，只有該函數內的程式會在嚴謹模式下執行。

```javascript
var chang = 100; // 將變數chang改成99
chag = 99; // 拼錯字
console.log(chang); // 100

("use strict");
var chang = 100;
chag = 99; // // ReferenceError: chag is not defined
```

- 嚴謹模式需要明確的宣告，未明確宣告`this`也會失效，這樣比較不會因為拼錯字而產生污染
- 在非嚴謹模式下如果沒有用 var 宣告變數，而直接賦值，會直接將此變數作宣告
- 嚴謹模式下並不會幫你執行，程式完全不跑

### 賦值運算子

- 賦值
- 賦予左方運算元與右方運算元相同之值。`x = y` 會把`y`的值賦予給`x`。

```javascript
x = y;
```

- 加法賦值

```javascript
x += y;
x = x + y;
```

- 減法賦值

```javascript
x -= y;
x = x - y;
```

### 比較運算子

`==`：等於

`!=`：不等於

- 如果運算元相同型別，就使用嚴格比較去檢驗。
- null 跟 undefined 相同。
- 運算元一個是數值，一個是字串，會將字串轉數字，再進行比較。
- 其中一個是 true 或 false 會轉成數字的 1 或 0，再進行比較。
- 其中一個是物件，另一個是字串或數值，物件會先轉成基型值，再進行比較。

`===`：嚴格等於

`!==`：嚴格不等於

- 先判斷運算元的型別是否相同，若不相同，結果為 false。
- null 與 undefined 都跟自己相等。
- true 與 false 都跟自己相等。
- NaN 不等於任何值，包括自己。
- 只要是 number 型別的值一樣，他們就相等。
- 0 跟-0 相等。
- string 長度跟內容不一樣，包括空白，它們就不相等。
- 如果參考至同一個物件、陣列、函式，相同的記憶體位置，他們就相等，若無，就算內容的值一樣，它們也不相等，不同的記憶體位置存相同的值。

```javascript=
console.log('1' === 1); //false
console.log(null === null); //true
console.log(undefined === undefined); //true
console.log(null === undefined); //false
console.log(NaN === NaN); //false
console.log(NaN !== NaN); //true
console.log('ABC' === 'ABC '); //false
```

`>`：大於

`>=`：大於等於

`<`：小於

`<=`：小於等於

注意：`=>`不是運算子，是箭頭函式。

### 算數運算子

- `%` 回傳兩個運算元相除後的餘數。

```javascript
count = 12 % 5;
console.log(count); // 回傳 2
```

- `++` 將運算元增加 1。

```javascript
x = 3;
x++;
console.log(x); // 回傳4，設定之後回傳

++x;
console.log(x); // 回傳3，回傳之後再設定
```

- `--` 將運算元減少 1。

```javascript
x = 3;
x--;
console.log(x); // 回傳2，設定之後回傳

--x;
console.log(x); // 回傳3，回傳之後再設定
```

### 邏輯運算子

- `&&` // `and` 前後全部為 `true` ，才會是 `true`，否則都返回 `false`
- `||` // `or` 前後只要一個是 `true` 就會返回 `true`，除非全部都是 `false`
- `!` // `not` 將後面的值做反向，如果是 `true` 就返回 `false`，如果是 `false` 就返回 `true`
- `!!` // `true`反轉再反轉，返回原本的布林值。
  大多用在轉換一些可以形成布林值的情況。
  而經過`!!`運算後，只會很單純出現`true` or `false`，可以單純化減少某些特殊情況出錯的機率。
  例如：希望"空字串"和`null`被視為完全相同時

  ```javascript
  const a = "";
  const b = null;

  a === b; // false
  !!a === !!b; // true
  ```

- 短路邏輯(短路解析)  
  Javascript 裡面只要是 `0`、`""`、`null`、`false`、`undefined`、`NaN` 都會被判定為 `false`

  - 用 || 來設定變數預設值  
    如果 obj 存在的話就維持原樣，如果不存在就給予空物件

    ```javascript
    if (!obj) {
      obj = {};
    }

    //短路邏輯的寫法
    var obj = obj || {};
    ```

  - 用 && 來檢查物件與屬性值

    ```javascript
    var name = o && o.getName();
    ```

  - 用 || 來簡化程式碼

    ```javascript
    if (!obj) {
      call_function();
    }
    obj || call_function();
    ```

  - 用 && 來簡化程式碼

    ```javascript
    var a = 1;
    if (a == 1) {
      alert("a=1");
    }
    a == 1 && alert("a=1");
    ```

  - 用 && 、|| 來簡化程式碼

    ```javascript
    var a = 3,
      b;
    if (a == 3) {
      b = 1;
    } else if (a == 5) {
      b = 2;
    } else {
      b = 3;
    }
    b = (a == 3 && 1) || (a == 5 && 2) || 3;
    ```

  - 善用 ! 的轉換

    ```javascript
    if (obj !== "null" && obj !== "undefined") {
      //....
    }
    if (!!obj) {
      //....
    }
    ```

### 其餘運算子與展開運算子 `...`

- 其餘運算子

  假設要將一個陣列的值相加後取平均

  ```javascript
  let arr = [1, 2, 3, 4, 5];

  let avg = function (arr) {
    let sum = 0;
    for (let i = 0; i < arr.length; i++) {
      sum += arr[i];
    }
    return sum / arr.length;
  };

  console.log(avg(arr)); //  3
  ```

  但若呼叫 function 時，不是傳入陣列，而是傳入多個參數

  最後得到的結果會是 NaN

  ```javascript
  let avg = function (arr) {
    let sum = 0;
    for (let i = 0; i < arr.length; i++) {
      sum += arr[i];
    }
    return sum / arr.length;
  };

  console.log(avg(1, 3, 5, 7, 9)); // NaN
  ```

  運用其餘運算子`...`，將輸入函式中的參數值變成陣列的形式

  ```javascript
  let avg = function (...arr) {
    console.log(arr); // [1,3,5,7,9]
    let sum = 0;
    for (let i = 0; i < arr.length; i++) {
      sum += arr[i];
    }
    return sum / arr.length;
  };

  console.log(avg(1, 3, 5, 7, 9)); // 5
  ```

- 展開運算子

  關鍵字與其餘運算子相同，但功能與其餘運算子相反，展開運算子可以把陣列中的元素取出。

  假設要用 `Math.max()` 來找出最大值，但傳入的參數為陣列，此時會得到 NaN

  ```javascript
  let number = [1, 2, 3, 4, 5, 6, 7, 8];

  console.log(Math.max(number)); // NaN
  ```

  運用展開運算子將陣列展開成許多數值

  ```javascript
  let number = [1, 2, 3, 4, 5];

  console.log(Math.max(...number)); // 5

  console.log(...number); // 1,2,3,4,5
  ```

### 三元運算式

```javascript
condition ? val1 : val2;
```

- 如果條件為 true ，此時回傳[數值 / 運算式（1）]
- 如果條件為 false，此時回傳[數值 / 運算式（2）]

```javascript
var status = "";
if (a < 60) {
  status = "不及格";
} else {
  status = "及格";
}

// 三元運算式
var status = a < 60 ? "不及格" : "及格";
```

### if else

```javascript
if (A) {
  //  A = True 執行這邊
} else if (B) {
  //  A = False and B = True 執行這邊
} else {
  //  A = False and B = False 執行這邊
}
```

- 判斷式括號裡會強制轉成布林值
- `null` 跟 `undefined` 和 `NaN` 在 if 判斷時值都會轉換為 `false`

## 流程判斷與迴圈

### switch

```javascript
switch (expression) {
  // expression => 表達式，用來跟每個case做比較
  case value1:
    //當 表達式 的值符合 value1
    //要執行的陳述句
    break;
  case value2:
    //當 表達式 的值符合 value2
    //要執行的陳述句
    break;
  default:
    //當 表達式 的值都不符合上述條件
    //要執行的陳述句
    break;
}
```

- 如果忘記放 break，則當下的 case 執行完之後，會直接往下一個 case 執行，直到遇見 break

```javascript
switch (表達式) {
  case x:
  case y:
  case z:
    //如果有多項條件，要執行同一陳述句可以合併撰寫
    // code block
    break;
  case a:
  case b:
  case c:
    // code block
    break;
}
```

### while 迴圈

```javascript
while ((A = x)) {
  陳述句; // 當條件式"A"成立的時候，就重複做"陳述句"
  break; // 遇到 break 就會停止，否則就會繼續執行，直到 A = F
}
```

- 完成之後再回去檢查"A"，成立就做"B"
- 直到"A"不成立，才會離開

```javascript
do {
  // 放要重複做的事情，會先執行一次再進入判斷
} while (判斷式);
```

### for 迴圈

```javascript
for (var i = 0; i < 10; i++) {
  // 要被執行的陳述句
  // for迴圈會產生出從"i = 0"開始到"i < 10"的長度
  // 可以當作計數器，每數一次就執行一次陳述句
}

// for (statement 1; statement 2; statement 3){}
// Statement 1 執行程式之前做一次
// Statement 2 執行程式的條件
// Statement 3 執行程式後每次執行

for (var i = 0; i < 10; i++) {
  // 也可以在迴圈裡加入判斷式取"i"的值
  if (i % 2 == 0) {
    continue; //跳過這次不做
  }
  if (i == 7) {
    break; //跳出整個for迴圈
  }

  console.log(i); // ans = "1  3  5" 7跟9已經跳出迴圈，所以不會被執行出
}
```

### for of

```javascript
var 物件 = ["A", "B", "C", "D"];
for (const 變數1 of 物件1) {
  // 大多使用在陣列，或是其他需要查找物件的內容物
}

let 字串 = "ES6"; //例如用來查找字串或數列等等
for (let 變數2 of 字串) {
  console.log(變數2); // "E", "S", "6"
}
```

### for in

```javascript
var 物件2 = {
  AA: 100,
  BB: 20,
};
for (變數3 in 物件2) {
  //會逐一尋找物件裡的屬性
  //且只顯示有值的資料出來
  console.log(物件2); //此時出現的是物件裡的字串
  console.log(物件2[變數3]); //此時出現的是物件裡的數值
}

var 物件3 = [10, 20, 30];
物件3[6] = 999; //這時物件3的陣列[10, 20, 30, nill]
```

## 函式 function

> function 是物件的一種

### 定義函式

- 函式宣告

  ```javascript
  function name(params) {
    // do some things
  }
  ```

- 函式運算式

  透過匿名函式將變數賦值

  ```javascript
  var squsre = function (params) {
    return params;
  };
  ```

  若在 function 加上名稱時，這個名稱只在"自己函式的區塊內有效"

  ```javascript
  var square = function func(number) {
    console.log(typeof func); // "function"
    return number * number;
  };

  console.log(typeof func); // undefined
  ```

- 透過 new 關鍵字建立函式

```javascript
// F 要大寫
var square = new Function("number", "return number * number");
```

透過關鍵字建立的函式物件，每次執行時都會進行解析字串的動作(如：`'return number * number'`)

### 箭頭函式

```javascript
function test(a) {
  return a + 1; // 將物件、運算結果傳出到呼叫點
}

var test = function (a) {
  return a + 1;
};

var test = (a) => {
  return a + 1;
};

var test = (a) => a + 1; //最終省略了function和return

var answer = test(5); //呼叫點，test(5)會將刮號內的參數傳到function的刮號(a)中
```

- 箭頭函式僅用於 function 內只有一條運算式時

### 變數的有效範圍 (Scope)

> 全域變數和區域變數的差異

```javascript
var x = 1;

var someThingHappend = function (y) {
  var x = 100;
  return x + y;
};

console.log(someThingHappend(50)); // 150
console.log(x); // 1
```

切分變數有效範圍的最小單位是 `function`

因此在 function 中透過 var 宣告的變數，其作用範圍僅限於這個函式。

此例中在一開始宣告的變數 x 與在 function 內部宣告的變數 x 為兩個不同變數。

若 function 中沒有宣告新變數，則會一層一層往外尋找，直到全域變數為止

```javascript
var x = 1;

var doSomeThing = function (y) {
  x = 100;
  return x + y;
};

console.log(doSomeThing(50)); // 150
console.log(x); // 100
```

此例中，function 中未宣告新變數 x，因此 javascript 向外層尋找同名的變數，直到最外層的全域變數，並將其賦值。

### 提升(Hoisting)

- 變數提升

  當 Scope 中的變數有被宣告，即使在宣告之前即調用變數，javascript 會將先告的語法拉到此 scope 的上面

  ```javascript
  var x = 1;

  var doSomeThing = function (y) {
    console.log(x); // undefined

    var x = 100;
    return x + y;
  };

  console.log(doSomeThing(50)); // 150
  console.log(x); // 1
  ```

  對編譯器而言此，這段程式碼會是這個樣子

  ```javascript
  var x = 1;

  var doSomeThing = function (y) {
    var x; // 宣告的語法被拉到上面
    console.log(x); // undefined
    x = 100;
    return x + y;
  };

  console.log(doSomeThing(50)); // 150
  console.log(x); // 1
  ```

- 函式提升

  透過"函式宣告"方式定義的函式可以在宣告前使用

  ```javascript
  square(2); // 4

  function square(number) {
    return number * number;
  }
  ```

  而透過"函式運算式"定義的函式則是會出現錯誤

  ```javascript
  square(2); // TypeError: square is not a function

  var square = function (number) {
    return number * number;
  };
  ```

  除呼叫時機不同，此兩者在執行時無明顯差異

### 全域變數

其實在 javascript 中並無所謂"全域變數"，所謂全域變數指的是"全域物件"(亦稱作"頂層物件")的屬性。

以瀏覽器而言，全域物件指的是 `window`，在 node 的環境中則叫做 `global`。

- 全域物件的屬性

  於外層透過 var 宣告一個變數 a，當我們調用 `window.a` 會回傳我們宣告的此一變數

  ```javascript
  var a = 10;
  console.log(a); // 10
  ```

- 變數的作用範圍，最小的的切分單位為 function
- 即使是寫在函式中，沒有 var 宣告的變數，會變成全域變數
- 全域變數指的是全域物件(頂層物件)的屬性

> ### 附註
>
> 在 javascript ES6 之後有新的宣告方法 let 與 const，分別定義"變數"與"常數"
> 和 var 不同的是，他們的作用區域是透過大括號`{}`來切分的

## 方法

### 取得 base_url

- 在 html 加入 meta 標籤

  ```html
  <head>
    <meta name="base-url" content="{{ url('/') }}" />
  </head>
  ```

- 此時就可以透過 meta 標籤取得 base_url

  ```javascript
  window.base_url = document.head.querySelector('meta[name="base-url"]');
  ```

### document

#### `.querySelector()` 元素選擇器

用法和 css 一樣，選取 id 元素時用 `#`，選取 class 元素時用 `.`

```javascript
document.querySelector(".title");
```

#### `.querySelectorAll()` 選取所有指定元素

用法和 `.querySelector()` 一樣，但不同於 `.querySelector()`，`.querySelectorAll()` 可以一次選取所有具有相同元素的內容

### prototype.forEach()

forEach 會修改原始陣列，且不會回傳值

```javascript
var forEachIt = people.forEach(function (item, index, array) {
  console.log(item, index, array); // 物件, 索引, 全部陣列
  return item; // forEach 沒在 return 的，所以這邊寫了也沒用
});
console.log(forEachIt); // undefined

people.forEach(function (item, index, array) {
  item.age = item.age + 1; // forEach 就如同 for，不過寫法更容易
});

console.log(people); // 全部 age + 1
```

### prototype.map()

使用 map 時會需要回傳一個值，他會透過函式內所回傳的值組成一個陣列。

- 如不回傳，則為 undefined
- 回傳長度等於原始長度

map 很適合將原始的變數運算後，重新組合成一個新的陣列。

使用語法：

```Javascript
const newArr = arr.map(function (value, index, array){
  //...
});
```

- `newArr` 處理後的新陣列
- `arr` 要執行 map 的舊陣列
- `function` 舊陣列中每個元素要執行的函式
- `value` 陣列中正在處理的值
- `index` 陣列中正在處理的 key 值(可略)
- `array` 舊陣列(可略)

```javascript
let A = [9000, 8500, 5500, 6500];
let B = A.map(function (value, index, array) {
  return value * 2;
});

console.log(A); // [9000, 8500, 5500, 6500] - 原陣列不會被修改
console.log(B); // [18000, 17000, 11000, 13000] 回傳新陣列
```

### prototype.push()

添加一個或多個元素至陣列末端，並回傳陣列的新長度。

```javascript
arr.push(element1[, ...[, elementN]])
```

- `elementN` 欲添加至陣列末端的元素

### `Math.round()` 四捨五入

```javascript
Math.round(3.14); // 3
Math.round(5.49999); // 5
Math.round(5.5); // 6
Math.round("5.50001"); // 6
Math.round(-5.49999); // -5
Math.round(-5.5); // -5
Math.round(-5.50001); // -6

let data = 18.62645;
Math.round(data * 10) / 10; // 18.6
Math.round(data * 100) / 100; // 18.63
Math.round(data * 1000) / 1000; // 18.626
```

### `Array.prototype.filter()`

- `arr.filter(function(item, index, array), thisValue)`
  - `item` 必須，目前元素的值
  - `index` 可選，目前元素的 key
  - `array` 可選，目前元素屬於的陣列
  - `thisValue` 可選，物件作為該次呼叫時使用，傳遞給函數當作 `this` 的值，若省略，則 `this` 的值為 `undefined`

```javascript
var ages = [32, 33, 12, 40];

function checkAdult(age) {
  return age >= document.getElementById("ageToCheck").value;
}

function myFunction() {
  document.getElementById("demo").innerHTML = ages.filter(checkAdult);
}
```

### `Array.prototype.splice()` 新增刪除陣列中的元素

- `array.splice(start, deleteCount, item)`

  - start 必填，陣列中要開始改動的元素索引。若索引長度大於陣列長度，則實際開始的索引值會被預設為陣列長度。若索引值為負，則會從陣列中最後一個元素開始往前改動(起始為 -1)，若其絕對值大於陣列長度，則會被設為 0。
  - deleteCount 可選，欲刪除的原陣列數量的整數。若省略，或其值大於可以被刪除的元素數量(從 start 到陣列最後的長度)，則將從 start 開始到陣列最後一個元素全部刪除。若 deleteCount 為 0 或負數，則不會有元素被刪除。
  - item 1, item 2 可選，從 start 開始要加入的元素，若未傳入任何數值，則將 start 到 deleteCount 之間的元素刪除。

- 從 `[2]` 的位置開始，刪除 0 個元素並插入 `drum`

  ```javascript
  var myFish = ["angel", "clown", "mandarin", "sturgeon"];
  var removed = myFish.splice(2, 0, "drum");

  // myfish 為 ["angel", "clown", "drum", "mandarin", "sturgeon"]
  // removed 為 []，沒有任何元素被刪除
  ```

- 從 `[3]` 的位置開始，刪除一個元素

  ```javascript
  var myFish = ["angel", "clown", "drum", "mandarin", "sturgeon"];
  var removed = myFish.splice(3, 1);

  // removed 為 ["mandarin"]
  // myFish 為 ["angel", "clown", "drum", "sturgeon"]
  ```

- 從 `[0]` 開始，刪除兩個元素，並插入 "parrot", "anemone", "blue"

  ```javascript
  var myFish = ["angel", "clown", "trumpet", "sturgeon"];
  var removed = myFish.splice(0, 2, "parrot", "anemone", "blue");

  // myFish 為 ["parrot", "anemone", "blue", "trumpet", "sturgeon"]
  // removed 為 ["angel", "clown"]
  ```

### 物件取值、新增與刪除

#### 物件取值

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  members: {
    mother: "mom",
    father: "dad",
  },
};

console.log(family.name); // ma's family
console.log(family.members.mother); // mom
console.log(family["name"]); // ma's family
```

用中括號語法，允許以變數的方式取值

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  members: {
    mother: "mom",
    father: "dad",
  },
};
var a = "name";

console.log(family.a); // undefind
console.log(family[a]); // ma's family
```

`.` 語法是直接以字串的方式尋找該物件的屬性，而 family 物件並無 a 屬性。
但中括號中的語法是將變數 a 的值帶入，相當於 `family['name']`。

另外，在物件中的屬性一律是字串，因此可以允許各種數字或是特殊字元，但在 `.` 語法中，會受到許多限制。

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  memebers: {
    mother: "mom",
    father: "dad"
  },
  1: '1',
  '$-小名家': '$-小名家 string'
};

console.log(family.1) // 語法錯誤
console.log(family[1]) // 1

console.log(family.$-小名家) // 語法錯誤
console.log(family['$-小名家']) // $-小名家 string
```

執行物件中的方法，也可以用點語法或是中括號

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  memebers: {
    mother: "mom",
    father: "dad",
  },
  callFamily: function () {
    console.log("call 2 ma's family");
  },
};

family.callFamily(); // call 2 ma's family
family["callFamily"](); // call 2 ma's family
```

#### 物件轉為陣列

陣列本身舉有許多好用的方法：`forEach`, `map`, `reduce`, `find`...，但物件無法使用這些陣列方法

利用 `Object` 關鍵字，將物件轉為陣列。

- Object.values 可以直接傳入一個物件，並將物件直接轉為陣列的形式，但無法取得 key 值。
- Object.keys 傳入一個物件，並將其 key 值以陣列方式呈現，僅只取 key 值。
- Object.entries 傳入物件，並同時回傳 key 值與 values，但產生的新結構，會另外用一層陣列組成。

#### 新增物件屬性

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  memebers: {
    mother: "mom",
    father: "dad",
  },
  callFamily: function () {
    console.log("call 2 ma's family");
  },
};

family.dog = "小豬";
family["kitten"] = "K Ka貓";
family["$"] = "money";
console.log(family);
```

#### 刪除物件屬性

使用 `delete` 關鍵字

```javascript
var family = {
  name: "ma's family",
  deposit: 1000,
  memebers: {
    mother: "mom",
    father: "dad",
  },
  callFamily: function () {
    console.log("call 2 ma's family");
  },
};

family.dog = "小豬";
family["kitten"] = "K Ka貓";
family["$"] = "money";

delete family.deposit;
delete family["$"];
console.log(family);
```

### SET 集合

#### 基本使用

- set 裡面的值是不會重複的

```javascript
// new Set Type
let classroom = new Set(); //  建立教室這個 set
let Aaron = { name: "Aaron", country: "Taiwan" };
let Jack = { name: "Jack", country: "USA" };
let Johnson = { name: "Johnson", country: "Korea" };

// 把物件放入 set 中
classroom.add(Aaron);
classroom.add(Jack);
classroom.add(Johnson);

// 檢驗 set 中是否包含某物件
if (classroom.has(Aaron)) console.log("Aaron is in the classroom");

//  把物件移除 set 中
classroom.delete(Jack);
console.log(classroom.size); //    看看 set 中有多少元素
console.log(classroom);
```

#### 陣列與集合間轉換

```javascript
// 集合轉成陣列
let setToArray = [...classroom]; // Array.from(classroom)

// 陣列轉成集合
let arrayToSet = new Set(setToArray);
```

#### 過濾陣列中重複的元素

利用 set 中元素不會重複的特性，來過濾掉陣列中重複的元素，留下唯一

```javascript
var mySet = new Set();

mySet.add(1); // Set { 1 }
mySet.add(5); // Set { 1, 5 }
mySet.add("some text"); // Set { 1, 5, 'some text' }
var o = { a: 1, b: 2 };
mySet.add(o); // Set { 1, 5, 'some text', { a: 1, b: 2 } }

// // o is referencing a different object so this is okay
mySet.add({ a: 1, b: 2 }); // Set { 1, 5, 'some text', { a: 1, b: 2 }, { a: 1, b: 2 } }
```

### JSON 轉換

- json 為一組字串
- 在使用 {} 建立物件時，屬性名稱的引號可以省略，但在 json 格式中，屬性名稱一定要有引號。
- 若物件中的值為 function 時，無法透過 json 傳遞。

#### `JSON.stringify()` 將物件轉為 json 字串

#### `JSON.parse()` 將 json 字串轉換為物件

## 額外補充

### randome(亂數)公式

```javascript
function getRandom(start, end) {
  return Math.floor(Math.random() * (end - start + 1)) + start;
}
var r = getRandom(0, 255);
var g = getRandom(0, 255);
var b = getRandom(0, 255);
```
