# Laravel 表單驗證

## 基礎驗證方法 `validate()`

```php
$validatedData = $request->validate([
    'title' => ['required', 'unique:posts', 'max:255'],
    'body' => ['required'],
]);
```

也可以使用 `validateWithBag()` 來請求驗證，並將所有錯誤訊息，儲存在一個命名錯誤訊息包

```php
$validatedData = $request->validateWithBag('post', [
    'title' => ['required', 'unique:posts', 'max:255'],
    'body' => ['required'],
]);
```

在驗證規則中，加入 bail，如果某個字串在第一次驗證失敗之後，就立即停止驗證。

```php
$request->validate([
    'title' => 'bail|required|unique:posts|max:255',
    'body' => 'required',
]);
// 如果title沒通過unique的規則，那麼就不會再 繼續驗證max規則
```

如果你的 HTTP 請求，包還嵌套參數(比如陣列)，你可以在驗證規則中使用「點」語法，來指定這些參數

```php
$request->validate([
    'title' => 'required|unique:posts|max:255',
    'author.name' => 'required',
    'author.description' => 'required',
]);
```

如果你的字段名稱包含點，則可以使用跳脫符號

```php
$request->validate([
    'title' => 'required|unique:posts|max:255',
    'v1\.0' => 'required',
]);
```

## [常用規則](https://learnku.com/docs/laravel/8.x/validation/9374)
