# Vue 學習筆記

- [Vue 學習筆記](#vue-學習筆記)
  - [Vue 實體的生命週期](#vue-實體的生命週期)
  - [Vue 屬性](#vue-屬性)
    - [監聽器(watch)](#監聽器watch)
      - [$watch](#watch)
      - [watch](#watch-1)
    - [計算(computed)](#計算computed)
      - [watch 和 computed 差別](#watch-和-computed-差別)
        - [依賴更新才會重新執行](#依賴更新才會重新執行)
    - [computed、watch 和 methods 的使用時機](#computedwatch-和-methods-的使用時機)
  - [eventHub 事件中心 (vue 2)](#eventhub-事件中心-vue-2)
  - [指令(directive)](#指令directive)
    - [屬性綁定](#屬性綁定)
    - [表單綁定 `v-model`](#表單綁定-v-model)
      - [input](#input)
      - [textarea 文字方塊](#textarea-文字方塊)
      - [radio](#radio)
      - [checkbox](#checkbox)
      - [select 下拉式選單](#select-下拉式選單)
    - [v-model 修飾子](#v-model-修飾子)
      - [.lazy](#lazy)
    - [模板綁定](#模板綁定)
      - [v-text](#v-text)
      - [v-html](#v-html)
      - [v-once](#v-once)
      - [v-pre](#v-pre)
    - [樣式綁定](#樣式綁定)
  - [條件渲染](#條件渲染)
    - [v-if](#v-if)
    - [v-show](#v-show)
  - [迴圈渲染](#迴圈渲染)
  - [事件監聽器](#事件監聽器)
  - [props](#props)
    - [命名與使用](#命名與使用)
    - [傳遞 props 值的方法](#傳遞-props-值的方法)
      - [傳遞字串](#傳遞字串)
      - [傳遞數字、布林值、陣列、物件](#傳遞數字布林值陣列物件)
    - [單向數據流](#單向數據流)
    - [改變子模組內的 prop 值](#改變子模組內的-prop-值)
    - [物件型別的 prop 傳遞](#物件型別的-prop-傳遞)
    - [子組件接收來自父組件的 props](#子組件接收來自父組件的-props)
      - [在模板中](#在模板中)
      - [在 data 中](#在-data-中)
      - [在 methods 中](#在-methods-中)
      - [在 mounted 中](#在-mounted-中)
      - [在 computed 中](#在-computed-中)
  - [VueX](#vuex)
    - [什麼是 VueX](#什麼是-vuex)
    - [帶入參數及呼叫方法](#帶入參數及呼叫方法)
      - [State](#state)
      - [mapState](#mapstate)
      - [Getters](#getters)
      - [mapGetters](#mapgetters)
      - [mutations](#mutations)
      - [mapMutations](#mapmutations)
      - [actions](#actions)
    - [專案結構](#專案結構)
  - [備註](#備註)
    - [判斷當前環境是否為開發環境](#判斷當前環境是否為開發環境)

## Vue 實體的生命週期

- `beforeCreate`: 當 Vue 實例初始化時便立即調用，此時尚未創建實例，因此所有 Vue 實體中的設定(如：data)都還未配置。
- `created`: 完成創建實例，此時 Vue 實體中的配置除了 \$el 外，其餘已全部配置，而 \$el 要在掛載模板後才會配置。
- `beforeMount`: 在 Vue 實體中被掛載到目標元素之前調用，此時的 \$el 依然未被 Vue 實體中的定義渲染的初始設定模板。
- `mounted`: Vue 實體上的設置已經安裝上模板，此時 \$el 是已經藉由實體中的定義渲染成真正的頁面。
- `beforeUpdate`: Vue 實體中的 data 產生變化後，或是執行 vm.$forceUpdate() 時調用，此時頁面尚未被重新渲染成變過的畫面。
- `update`: 在重新渲染頁面後調用，此時的頁面已經被重新渲染成改變後的畫面。
- `beforeDestroy`: 在此實體被銷毀前調用，此時實體依然擁有完整的功能。
- `destroyed`: 於此實體被銷毀後調用，此時實體中的任何定義(data, methods...)都已被解除綁定，在此做任何操作都會失效。

## Vue 屬性

### 監聽器(watch)

當資料變化時調用函數，函數會有兩個傳入參數：改變前的值、改變後的後的值，可以使用這個函數做跟此資料變化有的處理。

監聽器在 vue.js 中有兩種使用方式：

- `$watch` 實體上的函數，使用此函數註冊監聽器。
- `watch` 實體上的屬性，此屬性設置的物件在實體建立時會調用 `$watch` 註冊監聽器。

`$watch` 是註冊監聽器的函數，而 watch 是為了開發者方便在實體上設置監聽器而提供的，其實 watch 本身也是使用 $watch 註冊監聽器。

#### $watch

```javascript
unwatched = vm.$watch(expOrFn, callback, [options]);
```

`$watch` 的回傳值是註銷監聽器的函數，執行此函數可使監聽器失效。

- `exOrFn` 設定要監聽的目標，可以使用 javascript 表達式或是一個回傳監聽目標值的函數
- `callback` 當數值改變時，要叫用的函數，此函數會有兩個傳入參數：callback(newVal, oldVal)
  - `newVal` 改變後的資料值
  - `oldVal` 改變前的資料值
- `[options]` 非必要參數，監聽器的設定
  - `deep` 監聽物件時，物件下層屬性變化也會觸發監聽器
  - `immediate` 在實體初始畫設置監聽器的時候馬上叫用 callback 函數

```html
<div id="app">
  <button @click="a++">+</button>
  <button @click="a--">--</button>
  <div>a: {{a}}</div>
  <div>changed: {{newA}}</div>
  <div>before change: {{oldA}}</div>
</div>
```

```javascript
var vm = new Vue({
  ...
  data: {
    a: 1,
    newA: 0,
    oldA: 0
  }
});

vm.$watch('a', function(newA, oldA) {
  this.newA = newA;
  this.oldA = oldA;
});
```

#### watch

```javascript
watch: (
  key: value,
  ...
)
```

- 以 watch 為 key 值，下面定義的屬性都是欲監聽的資料來源。
- key 監聽目標名稱，可以使用 javascript 表達式
- value callback 函數的設定，共有 string, function, object 及 array 可以設定。
  - string callback 函數名稱
  - function callback 函數
  - object 設定監聽物件，設定方法如下
    - handler callback 函數
    - deep 布林值，是否監聽物件下層屬性
    - immediate 布林值 使否在實體初始化時立即調用 callback
  - array 當有多個監聽器時，使用陣列帶入多個 callback 函數

### 計算(computed)

和 watch 一樣，都是用來監聽數據的方式，但使用場景不同。

computed 是一個計算屬性，他根據依賴的資料，動態計算出一個新的值，並且會自動存入快取。當依賴的資料發生變化時，computed 會自動重新計算。這樣可以有效避免重複計算和提高性能。

computed 通常用計算衍生的資料，例如從一個列表中過濾出符合條件的資料，或根據資料的狀態產生顯示內容等等。在模板中，可以像普通的資料屬性一樣使用 computed

```javascript
export default {
  data() {
    return {
      count: 0,
    };
  },
  computed: {
    doubleCount() {
      return this.count * 2;
    },
  },
};
```

`doubleCount` 為一個計算屬性，他依賴於 count 屬性。當 count 屬性發生變化時，doubleCount 會自動重新計算。在模板中，可以像下面這樣使用：

```html
<p>count: {{ count }}</p>
<p>double count: {{ doubleCount }}</p>
```

#### watch 和 computed 差別

computed 最大特點是必須回傳一個值，並且將其存入快取，當方法中的依賴改變時，才會重新執行和求值。

但 watch 和 methods 不會強制要求回傳一個值，他們只需要執行動作，不一定要回傳值。

watch 會偵測單一個值，當她有變化時就執行。methods 只要呼叫，就會執行。

- computed 的特點
  - 當元件被建立時(created 生命週期)，computed 方法會被建立和執行一次。之後如果依賴沒有更新，就不會重新執行和求值，僅回傳快取的值。
  - computed 只能被該 computed 修改，不能被其他方法修改。例如：this.some_computed_function = 123 就會報錯。
  - computed 的方法必須回傳一個值。
  - computed 方法無法傳入參數

##### 依賴更新才會重新執行

> vue 官方文件
>
> 计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。

響應式依賴：在一個 computed 方法中，他所用到在 data 建立的資料，當資料產生變化，此方法就會重新執行和求值。

```javascript
computed:{
    total(){
        return this.price * this.quantity * this.discount
    }
}
```

total 的依賴就是 this.price, this.quantity, this.discount。只要其中一樣產生變化，就會重新執行 total()，並回傳新的值。

當 computed 內所有的依賴都沒有發生變化，此 computed 函示就會一直回傳之前儲存起來的值。

```javascript
<div id="app">
  <button @click="num = 1">按我改num</button>
  <p> 用add方法把以下的值由0變1：</p>
  <p> {{ add }} </p>
</div>
```

```javascript
// 當num變成1之後，changeOne()就不會再觸發，而「我有被觸發了！」這句也不會印出來

import { createApp } from "https://cdnjs.cloudflare.com/ajax/libs/vue/3.0.9/vue.esm-browser.js";
createApp({
  data() {
    return {
      num: 0,
    };
  },
  computed: {
    add() {
      console.log("我有被觸發了！");
      return this.num;
    },
  },
}).mount("#app");
```

當元件剛建立時(created)時，會打印一次，然後第一次按下按鈕時，會在打印一次，並且 num 會變成 1。
但第二次之後按下按鈕，就不會再觸發 add() 方法，因為每次按下按鈕，都會將 num 賦值為 1，和之前快取儲存的值相同。

### computed、watch 和 methods 的使用時機

當三者都能實現同一效果，但 computed 的效能較好

## eventHub 事件中心 (vue 2)

在無關聯的組件之間，互相傳遞 data

在需要取得 data 的組件上設置一個監聽器，每次要傳遞 data 時，那個組件就會廣播這個事件並調用這些監聽器。

eventHub 最主要的功能就是**監聽**和**廣播**

若 vue 搭配其他框架時，在 library 新增一個 eventHub.js

```javascript
import Vue from "vue";
const eventHub = new Vue();
export default eventHub;
```

若只有單純 vue 框架，則在頂層組件的 `data` 裡初始化 eventHub，並使用 `provide` 對外傳遞這個 eventHub

```javascript
import Vue from "vue";

export default {
  name: "App",
  components: {
    GrandParent,
  },
  data() {
    return {
      eventHub: new Vue(),
    };
  },
  provide() {
    return {
      eventHub: this.eventHub,
    };
  },
  methods: {
    setRandomValue() {
      this.eventHub.$emit("update:msg", Math.random() * 100);
    },
  },
};
```

在要傳遞 data 的組件裡加入廣播

```javascript
import eventHub from "../library/eventHub";
export default {
  data() {
    return {
      name: "",
    };
  },
  methods: {
    getCategories: function () {
      let id = "";
      axios
        .get(base_url + "/api/category/")
        .then((response) => {
          this.name = response.data.name;
          eventHub.$emit("categoryupdate", this.name);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
  created() {
    this.getCategories();
  },
};
```

接著在需要監聽的組件裡注入這個依賴，並在添加事件監聽。

```javascript
import eventHub from "../library/eventHub";
export default {
  data() {
    return {
      categories: [],
    };
  },
  mounted() {
    eventHub.$on("categoryupdate", this.categoryupdate);
  },
  methods: {
    categoryupdate(input) {
      this.categories.push(input);
    },
  },
};
```

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
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
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

- 如果要控制表單的全選或全部取消，只要控制 data 內的 checkedNames 陣列內容即可

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

```html
<button v-for="(item, index) in data.links">{{ item.label }}</button>
<!--  輸出結果 -->
&laquo; Previous
```

```html
<button v-for="(item, index) in data.links" v-html="item.label"></button>
<!--  輸出結果 -->
<< Previous
```

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

和 `v-if` 用法類似，不同的是 `v-show` 的元素始終會被渲染並保留在 DOM 中，`v-show` 只是單純的切換元素的 CSS property display。

`v-if` 是真正的條件渲染，他會確保在切換過程中條件內的事件監聽器和子組件適當的被銷毀和重建。

同時 `v-if` 也是惰性的，若在初始渲染時條件為 false，則不執行，直至條件第一次轉為 true 時，才會開始渲染。

相較之下，`v-show` 就比較單純，無論初始條件，元素總是會被渲染，`v-show` 做的只是基於 CSS 進行切換。

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

在 v-for 中可以訪問所有父作用域的 property。v-for 還可加入可選的第二參數作為當前的 key 值。

```html
<ul id="example-2">
  <li v-for="(item, index) in items">{{ parentMessage }} - {{ index }} - {{ item.message }}</li>
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
<div v-for="(value, name, index) in object">{{ index }}. {{ name }}: {{ value }}</div>
```

```log
0. title: How to do lists in Vue
1. author: Jane Doe
2. **publishedAt**: 2016-04-10
```

## 事件監聽器

> 靜態事件監聽
>
> - 元素上使用 v-on 監聽原生事件
> - 父組件設定 v-on 設定所需要監聽的事件，子組件用 $emit 觸發事件
> - 在 Vue 實體上設定生命週期鉤子，監聽各個鉤子事件。

當要在執行時去動態增減事件的監聽，這時就要用到 $on, $once, and $off 這些 js 函式來做設定。

## props

### 命名與使用

可以使用 PascalCase 或是 camelCase 的命名方法，但在 html 中必須使用 kebab-case 且應該小寫(html 大小寫不敏感)

像是 PostTitle 、 CartItem 、 TodoItem 等，在 HTML 中使用時就會變成 post-title 、 cart-item 、 todo-item。

```vue
<div id="vm">
<!--post-title 跟 post-content 都是props -->
  <blog-post post-title="Blog1" post-content="I\'m content1"></blog-post>
</div>

<script>
  Vue.component("blog-post", {
    props: ["PostTitle", "postContent"],
    template: `<div>
    <h3>{{ PostTitle }}</h3>
    <div>{{ postContent }}</div>
  </div>`,
  });
</script>
```

### 傳遞 props 值的方法

#### 傳遞字串

```vue
<blog-post post-title="Blog1" post-content="I\'m content1" post-complete="true" post-total-num="500" post="{title:'Blog1'}"></blog-post>
```

只要是直接傳遞(靜態傳遞)都是字串，所以 prop 接收的值 log1、I\'m content1、true、500、{...} 等等都是字串。

#### 傳遞數字、布林值、陣列、物件

利用 vue 的 v-bind 傳遞字串以外的值。

```vue
<blog-post
  post-title="動態傳遞"
  post-content="I\'m content1"
  v-bind:post-complete="true"
  v-bind:post-total-num="500"
  v-bind:post="{ title: '動態傳遞' }"
></blog-post>
```

也可以透過給予變數來獲得數字、布林值、陣列或物件等型別

```vue
<blog-post
  :post-title="postTitle"
  :post-content="postContent"
  :post-complete="postComplete"
  :post-total-num="postTotalNum"
  :post="post"
></blog-post>

<script>
  const vm = new Vue({
    el: "#vm",
    data: {
      postTitle: "動態傳遞",
      postContent: "I'm content",
      postComplete: true,
      postTotalNum: 500,
      post: { title: "動態傳遞" },
    },
  });
</script>
```

### 單向數據流

prop 是為了接收從富組件傳遞過來的資料，而這些資料是單向綁定的，已就是說父模組資料的更新，會影響子模組裡的 prop，但子模組裡 prop 值改變並不會影響父模組。

```vue
<prop-change :counter="counter"></prop-change>
<br />
<span>外 {{counter}}</span>
<button type="button" @click="changeOuterCounter">改變外面數字</button>

<script>
  Vue.component("prop-change", {
    props: ["counter"],
    template: `<div>
    <span>component內的  {{counter}}</span>
    <button type="button" @click="changeInnerCounter">改變component數字</button>
  </div>`,
    methods: {
      changeInnerCounter() {
        this.counter += 2;
      },
    },
  });

  const vm = new Vue({
    el: "#vm",
    data: {
      counter: 1,
    },
    methods: {
      changeOuterCounter() {
        this.counter += 1;
      },
    },
  });
</script>
```

以上測試可以得知：

- 外面(父層)的資料 counter 改變會影響子模組 prop 的 counter 的值。
- 子模組 prop 的 counter 值改變僅影響內部 counter 值
- 不論子模組的 prop 的 counter 值是否有變動，只要父模組資料 counter 改變時，子模組 prop 的 counter 值一定會連動。

### 改變子模組內的 prop 值

- 在 data 內創建一個值
  賦予 data 跟 prop 初始值相同的值，且之後也是針對該 data 內的值操作，並且不會再受到該 prop 的影響了

  ```javascript
  Vue.component("one-way-data", {
    props: ["counter"],
    template: `<div>
      <span>component內的  {{newCounter}}</span>
      <button type="button" @click="changeNewCounter">改變component數字</button>
    </div>`,
    data() {
      return {
        newCounter: this.counter,
      };
    },
    methods: {
      changeNewCounter() {
        this.newCounter += 10;
      },
    },
  });
  ```

### 物件型別的 prop 傳遞

- 父層透過標籤傳遞參數

```javascript
<ExLogLineComponent :channel-names="channel_names" :region-id="region.id" :bx-mac="region.bx_mac"></ExLogLineComponent>
```

- 子層 prop 接收參數後，透過 watch 將參數存入 data.return

```javascript
props: ['channelNames', 'regionId', 'bxMac'],
    data() {
        return {
            channel_names: [],
            region_id: '',
            mac: ''
        }
    },
    watch: {
        channelNames(names) {
            this.channel_names = names;
        },
        regionId(id) {
            this.region_id = id;
        },
        bxMac(mac) {
            this.mac = mac;
        }
    },
```

### 子組件接收來自父組件的 props

#### 在模板中

```vue
<template>
  <div>{{ parentData }}</div>
</template>

<script>
  export default {
    props: ["parentData"],
  };
</script>
```

#### 在 data 中

```vue
<script>
  export default {
    props: ["parentData"],
    data() {
      return {
        parent_data: this.parentData,
      };
    },
  };
</script>
```

#### 在 methods 中

```vue
<script>
  export default {
    props: ["parentData"],
    methods: {
      printParentData: function () {
        console.log(this.$props.parentData);
      },
    },
  };
</script>
```

#### 在 mounted 中

```vue
<script>
  export default {
    props: ["parentData"],
    mounted() {
      console.log(this.message);
    },
  };
</script>
```

#### 在 computed 中

```vue
<template>
  <div>
    <p>{{ parentDataLength }}</p>
  </div>
</template>

<script>
  export default {
    props: ["parentData"],
    computed: {
      parentDataLength() {
        return this.parentData.length;
      },
    },
  };
</script>
```

## VueX

> 參考資料：
>
> [Vuex 是什麼? 怎麼用?](https://medium.com/itsems-frontend/vue-vuex5-sumup-c170d4bd6c42)

### 什麼是 VueX

一個專門為 vue 專案開發的資料管理套件，可以為網站做全域的資料管理。

在專案結構下面可能會有多個組件，組件中又會有組件，組件的溝通，通常會會透過 emit 和 prop，而為了處理大型專案的兄弟組件間的溝通，VueX 就這樣誕生了。

VueX 有一點像是全域的 components，大家可以拿他資料，呼叫他出來用。

state 就像是 data，action + Mutation 就像是 methods，getters 就像 computed

在 VueX 中，儲存的狀態為 State，Component 使用 Dispatch 呼叫 Actions，讓 Actions 發出 commit 觸發 Mutations 去修改 State 的資料。整個 VueX 的方法也稱為 store。

### 帶入參數及呼叫方法

#### State

- 定義：/store.js

  ```javascript
  import Vue from "vue";
  import Vuex from "vuex";
  Vue.use(Vuex);

  const store = new Vuex.Store({
    state: {
      isLoading: false,
    },
  });
  export default store;
  ```

- 使用：/app.vue

  ```vue
  <template>
    <div id="app">
      <p>isLoading: {{ ifLoading }}</p>
    </div>
  </template>

  <script>
    export default {
      name: "app",
      computed: {
        // 注意: 這邊的ifLoading跟store的state的isLoading名字不同，是可以自定義的喔
        ifLoading() {
          return this.$store.state.isLoading;
        },
      },
    };
  </script>
  ```

#### mapState

- 定義：/store.js

  ```javascript
  import Vue from "vue";
  import Vuex from "vuex";
  Vue.use(Vuex);

  const store = new Vuex.Store({
    state: {
      myName: "Emma",
      isLoading: false,
    },
  });

  export default store;
  ```

- 使用：/app.vue

  ```vue
  <template>
    <div id="app">
      <p>My Name is {{ myName }}</p>
      <p>isLoading: {{ isLoading }}</p>
    </div>
  </template>

  <script>
    import { mapState } from "vuex";
    export default {
      name: "app",
      computed: {
        // 陣列寫法
        ...mapState(["isLoading", "myName"])
        // 物件寫法
        ...mapState({
          isLoading: state => state.isLoading,
          myName:  state => state.myName,
        })
      }
    };
  </script>
  ```

#### Getters

- 可帶參數：state, getters
- 定義：/store.js

  ```javascript
  import Vue from "vue";
  import Vuex from "vuex";
  Vue.use(Vuex);

  const store = new Vuex.Store({
    state: {
      myName: "Emma",
    },
    getters: {
      // 只有一個參數的箭頭函式寫法
      newName: (state) => {
        return state.myName + " lin";
      },
    },
  });

  export default store;
  ```

- 使用：/app.vue

  ```vue
  <template>
    <div id="app">
      <p>My New Name is {{ newName }}</p>
    </div>
  </template>

  <script>
    export default {
      name: "app",
      computed: {
        newName() {
          return this.$store.getters.newName;
        },
      },
    };
  </script>
  ```

加上參數 getters 表示可以呼叫別的 getters 來用

- 定義：/store.js

  ```javascript
  import Vue from "vue";
  import Vuex from "vuex";
  Vue.use(Vuex);

  const store = new Vuex.Store({
    state: {
      myName: "Emma",
    },
    getters: {
      newName: (state) => {
        return state.myName + " lin";
      },
      // 這邊呼叫下面那個getters
      anotherName: (state, getters) => {
        return getters.nickName;
      },
      nickName: (state) => {
        return state.myName + " Watson";
      },
    },
  });

  export default store;
  ```

- 使用：/app.vue

  ```vue
  <template>
    <div id="app">
      <p>My New Name is {{ newName }}</p>
      <p>or you can call me {{ anotherName }}</p>
    </div>
  </template>

  <script>
    export default {
      name: "app",
      computed: {
        newName() {
          return this.$store.getters.newName;
        },
        anotherName() {
          return this.$store.getters.anotherName;
        },
      },
    };
  </script>
  ```

#### mapGetters

定義和上面的 store.js 相同，差異在 components 呼叫的時候

- 使用：

  ```vue
  <script>
    import { mapGetters } from "vuex";
    export default {
      name: "app",
      computed: {
        // 陣列寫法
        ...mapGetters(["newName", "anotherName"])
        // 物件寫法
        ...mapGetters({
          newName: "newName",
          anotherName: "anotherName"
        })
      }
    };
  </script>
  ```

#### mutations

可帶參數：state, payload

- 使用：/app.vue

  ```javascript
  methods: {
    reverse() {
      this.$store.commit("Loaded");
    },
  }
  ```

- with payload:
  通常 payload 可以用物件表示，就能更具描述性，但是要記得在 mutations 運算時，帶入的參數 payload 要加上參數物件的 key

  - /app.vue

    ```javascript
    store.commit("addCounts", {
      amount: 10,
    });
    ```

  - /store.js

    ```javascript
    mutations: {
      addCounts (state, payload) {
        state.count += payload.amount
      }
    }
    ```

  - 所以帶上 payload 的呼叫可以這樣使用：

    ```javascript
    this.$store.commit("addTimes", 10);
    // 或
    this.$store.commit({
      type: "addTimes",
      count: 2,
    });
    ```

    物件 type 是必要的，其他可以隨意

#### mapMutations

```vue
<script>
  import { mapMutations } from "vuex";
  export default {
    name: "app",
    computed: {
      // 陣列寫法
      ...mapMutations(["Loaded", "addTimes"])
      // 物件寫法
      ...mapMutations({
        // add是component自定義的事件名稱，addTimes是mutations在store的名稱
        add: 'addTimes'
      })
    }
  };
</script>
```

使用 mapMutations，就要把 payload 直接帶入 template：

```html
<button @click="addTimes(2)">addTimes</button>
```

**注意**：mutations 一定只能同步執行，action 才能執行非同步

#### actions

- 可帶參數：
  - context: {commit, dispatch, state, getters, rootState, rootGetters}, payload

上面描述 mutations 只能同步執行，action 執行非同步，意旨 axios 要在 Actions 裡面做，不可以在 mutations 裡面做。

- 定義：/app.vue

  ```javascript
  ClickedActions({ commit }, payload) {
  commit('addTimes', payload)
  }
  ```

- 使用：/app.vue

  ```javascript
  methods: {
    add() {
      this.$store.dispatch("ClickedActions",2);
    }
  }
  ```

### 專案結構

1. App 層級的狀態要在 state 集中管理
   > app 層級的意思是指：不會因為跨組件改變的狀態，如：登入資訊、購物車清單...等。
   >
   > 會因為跨組件改變的狀態，如：下拉式選單的開關狀態、商品列表...等。
2. 唯一改變 state 的方式只有 mutations，而且是同步執行
3. actions 才可以非同步執行

```txt
|--index.js
|--main.js
|--api
|  |--...             // 後端 api
|--components         // 頁面
|  |--App.vue
|  |--...
|--store
   |--index.js        // 註冊 modules 並 export store
   |--actions.js      // 跨組件的 action
   |--mutations.js    // 跨組件的 mutations
   |--modules
      |--cart.js      // 購物車 model
      |--products.js  // 商品 model
```

## 備註

- 註 1: `Truthy` 假值(false, 0, -0, 0n, "", null, undefined, NaN)以外的任何值皆為 true

### 判斷當前環境是否為開發環境

```javascript
if (process.env.NODE_ENV !== "production") {
  this.is_dev = true;
} else {
  this.is_dev = false;
}
```

