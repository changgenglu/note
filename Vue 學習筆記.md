# Vue 學習筆記

## Vue 實體的生命週期

- `beforeCreate`: 當 Vue 實例初始化時便立即調用，此時尚未創建實例，因此所有 Vue 實體中的設定(如：data)都還未配置。
- `created`: 完成創建實例，此時 Vue 實體中的配置除了$el外，其餘已全部配置，而$el 要在掛載模板後才會配置。
- `beforeMount`: 在 Vue 實體中被掛載到目標元素之前調用，此時的$el 依然未被 Vue 實體中的定義渲染的初始設定模板。
- `mounted`: Vue 實體上的設置已經安裝上模板，此時$el 是已經藉由實體中的定義渲染成真正的頁面。
- `beforeUpdate`: Vue 實體中的 data 產生變化後，或是執行 vm.$forceUpdate() 時調用，此時頁面尚未被重新渲染成變過的畫面。
- `update`: 在重新渲染頁面後調用，此時的頁面已經被重新渲染成改變後的畫面。
- `beforeDestroy`: 在此實體被銷毀前調用，此時實體依然擁有完整的功能。
- `destoryed`: 於此實體被銷毀後調用，此時實體中的任何定義(data, methods...)都已被解除綁定，在此做任何操作都會失效。

## 指令(directive)

### 屬性綁定

透過 `v-bind`，進行數據綁定 HTML class

傳遞對象給 v-bind:class，用以動態切換 class

```html
<div v-bind:class="{ active: isActive }"></div>
```

此時 `active` 這個 class 是否存在，將取決於 property `isActive` 的 truthiness(註 1)。

可以在對象中傳入更多屬性，來動態切換多個 class。此外 v-bind:class 也可以與普通的 class attribute 共存

```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

data:

```javascript
data: {
  isActive: true,
  hasError: false
}
```

渲染結果

```html
<div class="static active"></div>
```

當 isActive 或 hasError 變化時，class 的屬性會同步更新。

例如：若 hasError 值為 true，class 屬性將變為 "static active text-danger"。

綁定的數據對象，不一定要定義在模板裡

```html
<div v-bind:class="classObject"></div>
```

```javascript
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

此渲染的結果和上面一樣。

也可以在此綁定 computed 屬性。

```html
<div v-bind:class="classObject"></div>
```

```javascript
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### 表單綁定 `v-model`

> 當使用 v-model 指令時，表單元素會自動忽略原有的 value, checked 和 selected 屬性，實際的值將以 data 內的狀態為主

#### input

在 input 文字框加入 v-model="message" 屬性之後，此文字框便會自動被綁定 input 事件

```html
<div id="app">
  <input type="text" v-model="message" />
  <p>Message is {{ message }}</p>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        message: "Hello",
      };
    },
  });
</script>
```

#### textarea 文字方塊

使用方式與 input 完全一樣

```html
<p><span>Multiline message is:</span>{{ message }}</p>

<textarea v-model="message"></textarea>
```

#### radio

```html
<div id="app">
  <div>
    <input type="radio" id="one" value="1" v-model="picked" />
    <label for="one">One</label>
  </div>
  <div>
    <input type="radio" id="two" value="2" v-model="picked" />
    <label for="two">Two</label>
  </div>

  <span>Picked: {{ picked }}</span>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        picked: 1,
      };
    },
  }).mount("#app");
</script>
```

因為 data 裡的 picked 預設為 1，所以執行時畫面上 `<input type="radio" id="one" value="1">` 會預設為已選擇

#### checkbox

可以當作多選的選項，而當他只有一個的時候，又可以將它做 boolean 的選項

複選時，用法跟前面 radio 完全一樣，因為是複選的關係，其差別在 data 內的狀態必須為陣列

```html
<div id="app">
  <input type="checkbox" id="jack" value="jack" v-model="checkedNames" />
  <label for="jack">jack</label>
  <input type="checkbox" id="john" value="john" v-model="checkedNames" />
  <label for="john">john</label>
  <input type="checkbox" id="mike" value="mike" v-model="checkedNames" />
  <label for="mike">mike</label>
  <input type="checkbox" id="mary" value="mary" v-model="checkedNames" />
  <label for="mary">mary</label>
  <br />
  <p>Checked names: {{ checkedNames }}</p>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        checkedNames: [],
      };
    },
  }).mount("#app");
</script>
```

- 如果要控制表單的全選或全部取消，只要控制 data 內的 cheakedNames 陣列內容即可

當 checkbox 為單選時

```html
<div id="app">
  <input type="checkbox" id="checkbox" v-model="isChecked" />
  <label for="jack">Status: {{ isChecked }}</label>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        isChecked: true,
      };
    },
  }).mount("#app");
</script>
```

此時， data 內的選項，會變成 true 或 false，當值為 true 時，對應的 checkbox 會被勾起。

#### select 下拉式選單

```html
<div id="app">
  <select v-model="selected">
    <option disabled value="">請選擇</option>
    <option>台北市</option>
    <option>新北市</option>
    <option>基隆市</option>
  </select>

  <p>Selected: {{ selected || '未選擇' }}</p>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        selected: "",
      };
    },
  }).mount("#app");
</script>
```

v-model 標籤須使用在 `<select>` 標籤，不能用在 `<option>` 標籤中

### v-model 修飾子

#### .lazy

```html
<input v-model.lazy="message" />
```

在 v-model 屬性後面加上.lazy，此輸入框就會從原本的 input 事件，變成監聽 change 事件

也就是，原本 input 事件會在輸入值時做實時的更新，而監聽 change 事件，則是當使用者離開輸入框焦點時才會更新。

### 模板綁定

#### v-text

```html
<div id="app">
  <div v-text="text"></div>
</div>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        text: "hello",
      };
    },
  }).mount("#app");
</script>
```

當透過 v-text 指令來進行綁定，此時畫面渲染出來的結果會與下面相同

```html
<div>{{ text }}</div>
```

但若在 v-text 綁定的標籤內加入文字，以 v-text 指令渲染出來的結果會無視標籤內的內容

```html
<!-- 只會出現 Hello -->
<div v-text="text">World!</div>

<!-- 出現 Hello world! -->
<div>{{ text }} world!</div>
```

#### v-html

和 v-text 類似，但當 data 的內容為 HTML 的語法時，v-html 會將其渲染為 html 語法

#### v-once

只渲染指定的節點一次，往後就不再更新

#### v-pre

加入 v-pre 後，就不會解析模板內容。

### 樣式綁定

## 條件渲染

### v-if

其屬性為 truthy，當其返回 true 時會被渲染。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

可以添加 `v-else`

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>It's not true.</h1>
```

還可以添加 `v-else-if`

此三元素需緊跟彼此，否則將不會被識別

### v-show

和 v-if 用法類似，不同的是 v-show 的元素始終會被渲染並保留在 DOM 中，v-show 只是單純的切換元素的 CSS property display。

v-if 是真正的條件渲染，他會確保在切換過程中條件內的事件監聽器和子組件適當的被銷毀和重建。

同時 v-if 也是惰性的，若在初始渲染時條件為 false，則不執行，直至條件第一次轉為 true 時，才會開始渲染。

相較之下，v-show 就比較單純，無論初始條件，元素總是會被渲染，v-show 做的只是基於 CSS 進行切換。

## 迴圈渲染

v-for 可以用陣列進行渲染成一個列表。其語法為 item in items，items 為源陣列，而 item 則為被迭代的陣列元素別名。

```html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">{{ item.message }}</li>
</ul>
```

```javascript
var example1 = new Vue({
  el: "#example-1",
  data: {
    items: [{ message: "Foo" }, { message: "Bar" }],
  },
});
```

輸出

```log
Foo
Bar
```

在 v-for 中可以訪問所有父作用域的 propert。v-for 還可加入可選的第二參數作為當前的 key 值。

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```javascript
var example2 = new Vue({
  el: "#example-2",
  data: {
    parentMessage: "Parent",
    items: [{ message: "Foo" }, { message: "Bar" }],
  },
});
```

```log
parent-0-Foo
parent-1-Bar
```

用 v-for 來迭代一個對象的 property

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">{{ value }}</li>
</ul>
```

```javascript
new Vue({
  el: "#v-for-object",
  data: {
    object: {
      title: "How to do lists in Vue",
      author: "Jane Doe",
      publishedAt: "2016-04-10",
    },
  },
});
```

```log
How to do lists in Vue
Jane Doe
2016-04-10
```

可以傳入第二個參數作為 property 的名稱(key 值)

```html
<div v-for="(value, name) in object">{{ name }}: {{ value }}</div>
```

```log
title: How to do lists in Vue
author: Jane Doe
publishedAt: 2016-04-10
```

還可以傳入第三個參數作為索引值

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

```log
0. title: How to do lists in Vue
1. author: Jane Doe
2. **publishedAt**: 2016-04-10
```

## 備註

- 註 1 Truthy: 假值以外的任何值皆為 true，即所有除了 false, 0, -0, 0n, "", null, undefined, NaN 以外的值皆返回 true

> > > > > > > 9a7bdcabf2afd9a9b7842fa652816a812ccaf637
