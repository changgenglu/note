# JavaScript 物件

> 物件(object)類型是電腦程式的一種資料類型，用抽象化概念來比喻人類現實世界中的物體。
>
> 在 javascript 中，除了原始的資料類型(數字、字串、布林等等)，所有的資料類型都是物件。不過 javascript 的物件與其他目前流行的物件導向程式語言有明顯不同，他一開始是使用原型基礎(prototype-based)的設計，而其他的物件導向程式語言，大部分都是使用類別基礎(class-based)的設計。
>
> 物件在 javascript 中可以分為兩種應用層面：
>
> - 主要用於資料的描述，他扮演類似關聯陣列的資料結構，儲存 key value 的成對資料。常見到用陣列中包含物件資料來代表複數的資料集合。
> - 主要用於物件導向的程式設計，可以設計出各種的物件，其中包含各種方法，就像已經介紹過的各種包裝物件，如：字串、陣列等等的包裝物件。
>   物件類型使用以屬性與方法為主要組成部分，這兩種和稱為物件成員(member)`:
>
> - 屬性：物件基本可被描述的量化資料。例如水果這個物件包含：顏色、產地、大小、重量、甜度等等屬性
> - 方法：物件的可被反應動作或行為。例如車子這個物件，他的行為有加速、剎車、轉彎、打方向燈等行為或可執行的動作。

## 物件定義的方式

### 物件字面(Object Literals)

用於資料描述的物件定義，使用大括號(`{}`)作為宣告區域，其中加入無關順序的"key-value"成對值，屬性的值可以是任何合法的值，可以包括陣列、函式、或其他物件。

而在物件定義中的"key-value"，如果是一般的值的情況，稱為`屬性(property/prop)`，如果是一個函式，稱之為`方法(method)`。屬性與方法我們通常和稱為物件中的`(member)`。

```javascript
const emptyObject = {};

const player = {
  fullName: "ivan",
  age: 16,
  gender: "man",
  hairColor: "black",
};
```

在定義與獲取值方面，物件和陣列的情況相當類似。

```javascript
const aArray = [];
const aObject = {};

const bArray = ["foo", "bar"];
const bObject = {
  firstKey: "foo",
  secondKey: "bar",
};

bArray[2] = "yes";
bObject.thirdKey = "yes";

console.log(bArray[2]); //yes
console.log(bObject.thirdKey); //yes
```

不過，對於陣列的有序索引值，而且只有索引值的情況，我們更加關心物件中`key`的存在。
物件中的 member(屬性與方法)，都是使用物件上加點`.`符號來獲取。

## 類別(class)

類別是先裡面定義好物件的整體結構藍圖，然後再用這個類別定義，來產生相同結構的東個物件實例，類別在定義時不會直接產生出物件，要經過實例化的過程(new 關鍵字)，才會產生真正的物件實例。另外，目前因為類別定義方式還是很新的語法，在實作時，除了比較新的函式庫或框架，才會開始使用他來撰寫。

```javascript
class Player {
  constructor(fullName, age, gender, hairColor) {
    this.fullName = fullName;
    this.age = age;
    this.gender = gender;
    this.hairColor = hairColor;
  }

  toString() {
    return "Name: " + this.fullName + ", Age:" + this.age;
  }
}

const inori = new Player("Inori", 16, "girl", "pink");
console.log(inori.toString());
console.log(inori.fullName);

const tsugumi = new Player("Tsugumi", 14, "girl", "purple");
console.log(tsugumi.toString());
```

### this

在這個物件的類別定義中，我們第一次見識到 this 關鍵字的用法，this 簡單來說，是物件實體專屬的指向變數，this 指向的就是"這個"物件實體。以上面的例子：當物件真正實體化時，this 變數會指向這個物件實體。this 是怎麼知道要指向哪一個物件實體？是因為 new 關鍵字造成的結果。

this 變數是 javascript 的一個特性，他是隱藏的內部變數之一，當函式呼叫或是物件實體化時，都會以這個 this 變數的指向對象作為執行期間的依據。

this 呼叫的情境：

1. 函式呼叫：在一般情況下的函式呼叫，this 通常都會指向全域物件(global object)
2. 建構式呼叫(construct)：透過 new 將物件實例化，等於呼叫類型的建構式，this 會指向新建立的物件實例
3. 物件中的方法呼叫：this 指向呼叫這個方法的物件實體

所以當建構式呼叫時，也就是使用 new 運算福建立物件時，this 會指向新建立的物件實例，也就是下面這段程式碼：

```javascript
const ivan = new Player("ivan", 16, "boy", pink);
```

因此在建構式中的指定值的語句，裡面的 this 值就會指向是這個新建立的物件，也就是`ivan`：

```javascript
constructor(fullName, age, gender, hairColor) {
        this.fullName = fullName
        this.age = age
        this.gender = gender
        this.hairColor = hairColor
    }
```

也就是說，在建立物件後，經建構式的執行語句，這個`ivan`物件中的屬性值就會被指定完成，所以可以用下面的語法來存取屬性：

```javascript
ivan.fullName
ivan.age
ivan.gender
ivan.hairColor
```

第三種情況是呼叫物件中的方法，也就是向下面的 code，this 會指向這個呼叫 toString 方法的物件，也就是 ivan

```javascript
ivan.toString();
```
