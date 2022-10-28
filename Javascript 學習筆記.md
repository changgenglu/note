# Javascript 學習筆記

###### tags: `前端` `Javascript`

- [Javascript 學習筆記](#javascript-學習筆記) - [tags: `前端` `Javascript`](#tags-前端-javascript)
  - [基本概念](#基本概念)
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
    - [prototype.forEach()](#prototypeforeach)
    - [prototype.map()](#prototypemap)
  - [函式 function](#函式-function)
    - [定義函式](#定義函式)
    - [箭頭函式](#箭頭函式)
    - [變數的有效範圍 (Scope)](#變數的有效範圍-scope)
    - [提升(Hoisting)](#提升hoisting)
    - [全域變數](#全域變數)
  - [額外補充](#額外補充)
    - [randome(亂數)公式](#randome亂數公式)

> ### 參考資料
>
> - [重新認識 javascript](https://ithelp.ithome.com.tw/users/20065504/ironman/1259)

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

- 將不同型態的物件通通轉為字串  
  \`${}\` 在大括號中加入變數

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

### 將物件轉換為陣列

```json
"params": {
  "Va": "115.4",
  "Vb": "116.9",
  "Vc": "117.1",
  "Vab": "200.8",
  "Vbc": "202.4",
  "Vca": "201.0",
  "EPS": "30798.85",
  "Ia": "11.760",
  "Ib": "20.880",
  "Ic": "13.520",
  "PS": "10.040",
  "F": "60.0",
  "PFS": "0.962",
  "EQS": "10067.65",
  "DM": "26.55",
  "EPa": "9461.48"
}
```

- object.values()
  可以直接傳入一個物件，並將傳入的物件直接轉為陣列的形式。

```javascript

```

## 方法

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
