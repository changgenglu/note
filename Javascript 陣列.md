# JavaScript 陣列

> 參考資料：
>
> [JavaScript Array 陣列操作方法大全 ( 含 ES6 )](https://www.oxxostudio.tw/articles/201908/js-array.html)

## 改變原始陣列

### push() 加入陣列最後一個位置

將值加入陣列的最後一個位置，push() 會回傳新的陣列長度。

```javascript
let arr = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(arr.push(9, 10)); // 10
console.log(arr); // [1, 2, 3, 4, 5, 6, 7, 8, ,9, 10]
```

### pop() 取出陣列的最後一個元素

```javascript
let arr = [1, 2, 3, 4, 5, 6];
let new_arr = arr.pop();
console.log(arr); // [1,2,3,4,5]
console.log(new_arr); // 6
```

### shift() 取出並移除陣列的第一個元素

```javascript
let arr = [1, 2, 3, 4, 5];
let new_arr = arr.shift();
console.log(arr); // [2, 3, 4, 5]
console.log(new_arr); // 1
```

### unshift() 將元素添加到第一個位置

```javascript
let arr = [1, 2, 3, 4, 5];
arr.unshift(100, 200, 300);
console.log(arr); // [100, 200, 300, 1, 2, 3, 4, 5]
```

### reverse() 反轉陣列

```javascript
let arr = [1, 2, 3, 4, 5];
arr.reverse();
console.log(arr); // [5, 4, 3, 2, 1]
```

### splice(start, delete_count, item) 新增或移除陣列中指定位置的元素

可以移除或新增陣列的元素，包含三個參數

- start 要編輯的序列號碼
- delete_count 要移除的長度(選填，若不填，則將第一個號碼位置面的所有元素清除，若為 0 則不刪除元素)
- item 要添加的內容(選填)

```javascript
let arr = [1, 2, 3, 4, 5, 6, 7, 8];
arr.splice(5, 1);
console.log(arr); // [1, 2, 3, 4, 5, 7, 8] (6 被移除了)
```

設定第三個參數就能添加或取代元素

```javascript
let arr = [1, 2, 3, 4, 5, 6, 7, 8];
arr.splice(5, 1, 100);
console.log(arr); // [1, 2, 3, 4, 5, 100, 7, 8]; (6 被移除，100 被加到第五的位置)
```

### sort() 針對陣列的元素進行排列

### copyWithin()

### fill()

## 回傳陣列元素資訊或索引值

### length()

### indexOf()

### lastIndexOf()

### find()

### findIndex()

### filter()

## 針對每個元素進行處理

### forEach()

## 產生新的陣列或值

### join()

### concat()

### slice()

### map()

### reduce()

### reduceRight()

### flat()

### flatMap()

### Array.from()

### Array.of()

### toString()

## 判斷

### every()

### some()

### includes

### Array.inArray()

## 其他

### keys()

### valueOf()
