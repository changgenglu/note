# CSS 學習筆記

- [CSS 學習筆記](#css-學習筆記)
  - [後裔選擇器](#後裔選擇器)
    - [基本類型](#基本類型)
    - [複合型](#複合型)
  - [屬性選擇器`[]`](#屬性選擇器)
  - [表格](#表格)
  - [偽元素](#偽元素)
  - [`display:none`和`visibility:hidden`的差別](#displaynone和visibilityhidden的差別)
  - [Display](#display)
    - [Display Outside](#display-outside)
      - [Block 區塊元素](#block-區塊元素)
      - [Inline 行內元素](#inline-行內元素)
    - [Display Inside](#display-inside)
      - [Table](#table)
      - [Flex](#flex)
        - [Flex-direction 方向性](#flex-direction-方向性)
        - [justify-content 調整內容](#justify-content-調整內容)
        - [align-items 對齊物件](#align-items-對齊物件)
        - [align-self 自身對齊](#align-self-自身對齊)
      - [Wrap 斷行](#wrap-斷行)
    - [Global 全域屬性](#global-全域屬性)
      - [inherit 跟隨父層屬性](#inherit-跟隨父層屬性)
      - [initial 變回原本屬性](#initial-變回原本屬性)
    - [Display-Box 影響用箱子裝起來的所有元素](#display-box-影響用箱子裝起來的所有元素)
      - [none](#none)
    - [Display-Legacy 此屬性繼承兩者的特性](#display-legacy-此屬性繼承兩者的特性)
      - [inline-block](#inline-block)
      - [inline-table](#inline-table)
      - [inline-flex](#inline-flex)

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

## Display

### Display Outside

#### Block 區塊元素

- 總是以新的一行開始，所以無論設定多少寬度，他基本容器的寬度，還是會撐滿整個空間。
- 即使容器中的元素已經被調整成 50%，但他還是會將後面的元素排擠在下面。

#### Inline 行內元素

- 又稱線內元素，元素本身高度多少他就是多少，無法調整寬高，此外他可以設定 padding 的上下左右，而 margin 只能設定他的左右。
- inline 屬性預設元素的排列為由左到右，直到裝滿容器。

### Display Inside

#### Table

- 可以將元素直接模擬成 table 來使用。

  - Table-Row 對應 `<tr>`
  - Table-Row-Group 對應 `<tbody>`
  - Table-Cell `<td>`
  - Table-caption `<caption`
  - Table-Column `<col>`
  - Table-Column-Group `<colgroup>`
  - Table-Header-Group `<thead>`
  - Table-Footer-Group `<tfooder>`

#### Flex

- 設定 flex 屬性之前，需先設定父層容器 display: flex

##### Flex-direction 方向性

- 水平方向(瀏覽器預設)：row, row-reverse(水平方向反轉)
- 垂直方向：column, column-reverse(垂直方向反轉)

##### justify-content 調整內容

- 改變 flex 物件在主軸上的對齊(預設為水平方向)，若 flex-direction 為 column，則對齊方向改為垂直方向(y 軸)
  - justify-content: flex-start 以起點為基準
  - justify-content: flex-end 以尾端為基準
  - justify-content: center 以中間為基準
  - justify-content: space-between 會將物件依容器大小均分
  - justify-content: space-around 會將物件依容器大小均分，並會給左右空間

##### align-items 對齊物件

- 改變橫軸上所有 flex 物件的對齊(預設為垂直方向)，若 flex-direction 為 column，則對其方向會改為水平方向(x 軸)
  - align-items: flex-start 以起點為基準
  - align-items: flex-end 以尾端為基準
  - align-items: center 以中間為基準
  - align-items: baseline 以物件基準線為基準
  - align-items: strrtch 以起點為基準，但會撐滿容器(瀏覽器預設)

##### align-self 自身對齊

- 單獨改變物件在橫軸上的對齊(預設為垂直方向)，若 flex-direction 為 column，則對齊方向則改為水平方向(x 軸)
  - align-seelf: flex-start 以起點為基準
  - align-seelf: flex-end 以尾端為基準
  - align-seelf: center 以中間為基準
  - align-seelf: baseline 以物件基準線為基準
  - align-seelf: strrtch 以起點為基準，但會撐滿容器(瀏覽器預設)

#### Wrap 斷行

- Flex-Nowrap：強制不斷行。
- Flex-Wrap：裝滿容器會強制斷行。
- Flex-Wrap-Reverse：裝滿容器會強制斷行，但排列順序是相反的。

### Global 全域屬性

- 除了 display 以外，齊他任意屬性都能使用

#### inherit 跟隨父層屬性

- 當父層屬性為 block，子層下 Display:Inherit 時，子層屬性也會變成 block

#### initial 變回原本屬性

- 假如我 div 屬性在某種情況下更改為 inline 屬性，那我後面有吃到同樣 CSS 的 div 想改回 Block，我只要下 Display:Initial，就會變回 Div 原本的 Block 屬性

### Display-Box 影響用箱子裝起來的所有元素

#### none

- 若被 display: none; 的 div 裝起來的元素，會被隱藏。

### Display-Legacy 此屬性繼承兩者的特性

#### inline-block

- 讓許多區塊自動浮起來水平排列，且不用額外設定 clear 也不會讓接著的元素浮上來蓋住區塊

#### inline-table

#### inline-flex

- flex 時父元素為 block，而 inline-flex 則是父元素變成 inline，他會根據子元素所有的 div 大小自適應寬度和高度
