# CSS 學習筆記

###### tags: `前端` `CSS`

- [CSS 學習筆記](#css-學習筆記) - [tags: `前端` `CSS`](#tags-前端-css)
  - [後裔選擇器](#後裔選擇器)
    - [基本類型](#基本類型)
  - [屬性選擇器`[]`](#屬性選擇器)
  - [表格](#表格)
  - [偽元素](#偽元素)

> 將所有物件加上外框
>
> ```css
> * {
>   outline: 1px solid #000;
> }
> ```

## 後裔選擇器

### 基本類型

- 標籤 `#id` `.class`

- div 標籤和 span 標籤

  ```css
  div,
  span {
    //
  }
  ```

### 複合型

- div 標籤底下，為 span 標籤

  ```css
  div > span {
    //
  }
  ```

- div 標籤內所有的 span 標籤

  ```css
  div span {
    //
  }
  ```

- div 標籤之後的第一個 span 標籤

  ```css
  div + span {
    //
  }
  ```

- div 標籤之後的所有 span 標籤

  ```css
  div ~ span {
    //
  }
  ```

## 屬性選擇器`[]`

可直接查找任何屬性內元素(ex:`class`, `div`, `title`, `href`,....)，亦可使用於直接指定屬性

- `|` 符號為屬性，包含`[foo]`且必在開頭，須為獨立字元、特定單詞，以及字元後加上連接符號 `-`

  ```css
  p[class|="red"] {
    //
  }
  ```

- `~` 符號為屬性只要有包含`[foo]`，無順序問題，需為獨立字元、特定單詞

  ```css
  a[herf~="apple"] {
    //
  }
  ```

- `^` 符號為屬性使用`[foo]`開頭，不特定獨立字元

  ```css
  a[herf^="http"] {
    //
  }
  ```

- `$` 符號為屬性使用`[foo]`結尾，不特定獨立字元

  ```css
  a[herf$="selectors.asp"] {
    //
  }
  ```

- `*` 符號為屬性內含有`[foo]`，不特定獨立字元

  ```css
  a[herf*="pseudo"] {
    //
  }
  ```

## 表格

- 將標籤做成表格

  ```css
  .class {
    display: grid;
    //
  }
  ```

## 偽元素

- 在原本的元素「之前」加入內容

  ```css
  ::before ;
  ```

- 在原本的元素「之後」加入內容

  ```css
  ::after ;
  ```

- 兩者都是以 display: inline-block;屬性存在
- 偽元素會「繼承」原本元素的屬性
- 偽元素一定要加上 content 的屬性，沒有 content 偽元素不會出現在畫面上

## `display:none`和`visibility:hidden`的差別

當使用 `visibility:hidden` 時，物件是確實的被隱藏的，但依然保有物件的位置

例如：當表格內的標籤加上 `visibility:hidden` 時，其儲存格中的值會被隱藏，但儲存格不會消失

當使用 `display:none` 時，物件及其原本的位置都會被隱藏
