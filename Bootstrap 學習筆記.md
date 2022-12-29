# Bootstrap 學習筆記

###### tags: `前端` `Bootstrap`

## html 引入

```html
<head>
  <meta charset="UTF-8" />
  <link rel="stylesheet" href="../_css/bootstrap.min.css" />
  <script src="../_js/jquery.min.js"></script>
  <script src="../_js/popper.min.js"></script>
  <script src="../_js/bootstrap.min.js"></script>
  <title>Document</title>
</head>
```

## 表格

```html
<div class="row">
  <div class="col-6"></div>
  <div class="col-6"></div>
</div>
```

## 部分顏色名稱

- primary 主要的
- secondary 副標
- success 成功
- danger 危險
- warning 警告
- info 訊息
- light 明亮(帶有淺邊框)
- dark 深色
- white 白色

## 改變字型的外觀

- font-weight-bold
- font-weight-normal
- font-weight-light
- fon-italic(斜體)

## 文字位置設定

- text-left
- text-center
- text-right

## 輪播牆

```html
<div id="mydemo1" class="carousel slide" data-ride="carousel">
  <ol class="carousel-indicators">
    <li data-target="#mydemo1" data-slide-to="0" class="active"></li>
    <li data-target="#mydemo1" data-slide-to="1"></li>
    <li data-target="#mydemo1" data-slide-to="2"></li>
  </ol>
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img src="https://dummyimage.com/1200x300/00ff5e/ffffff" alt="" />
    </div>
    <div class="carousel-item">
      <img src="https://dummyimage.com/1200x300/ff8400/ffffff" alt="" />
    </div>
    <div class="carousel-item">
      <img src="https://dummyimage.com/1200x300/f057c5/ffffff" alt="" />
    </div>
  </div>
  <a
    class="carousel-control-prev"
    href="#mydemo1"
    role="button"
    data-slide="prev"
  >
    <span class="carousel-control-prev-icon" aria-hidden="true"></span>
    <span class="sr-only">Previous</span>
  </a>
  <a
    class="carousel-control-next"
    href="#mydemo1"
    role="button"
    data-slide="next"
  >
    <span class="carousel-control-next-icon" aria-hidden="true"></span>
    <span class="sr-only">Next</span>
  </a>
</div>
```

## drop down

```html
<li class="nav-item dropup">
  <a class="nav-link active " id="navbardrop" data-toggle="dropdown">
    珈琲體驗
  </a>
  <div class="dropdown-menu">
    <a class="dropdown-item" href="#"> 手沖珈琲 </a>
    <a class="dropdown-item" href="#"> 珈琲特調體驗 </a>
    <a class="dropdown-item" href="#"> 拿鐵拉花 </a>
  </div>
</li>
```

