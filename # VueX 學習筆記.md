# VueX 學習筆記

> 參考資料：
>
> [Vuex 是什麼? 怎麼用?](https://medium.com/itsems-frontend/vue-vuex5-sumup-c170d4bd6c42)
>
> 實作：
>
> [github-repo: vuex-test-1](https://github.com/changgenglu/vuex-note)
> [github-pages: vuex-test-1](https://changgenglu.github.io/vuex-note/)

## 什麼是 VueX

一個專門為 vue 專案開發的資料管理套件，可以為網站做全域的資料管理。

在專案結構下面可能會有多個組件，組件中又會有組件，組件的溝通，通常會會透過 emit 和 prop，而為了處理大型專案的兄弟組件間的溝通，VueX 就這樣誕生了。

VueX 有一點像是全域的 components，大家可以拿他資料，呼叫他出來用。

state 就像是 data，action + Mutation 就像是 methods，getters 就像 computed

在 VueX 中，儲存的狀態為 State，Component 使用 Dispatch 呼叫 Actions，讓 Actions 發出 commit 觸發 Mutations 去修改 State 的資料。整個 VueX 的方法也稱為 store。

## 帶入參數及呼叫方法

### State

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

### mapState

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

### Getters

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

### mapGetters

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

### mutations

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

### mapMutations

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

### actions

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

## 專案結構

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
