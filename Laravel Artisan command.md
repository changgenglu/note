# Artisan Command

## 自訂 Artisan command

### 利用指令建立要自訂命令的檔案

```bash
php artisan make:command $name
```

建立的檔案位置會在 app/Console/Commands

### 編輯檔案

編輯指令名稱

```php
protected $signature = 'EditCity:city {country_code}';
```

實際指令

```bash
php artisan EditCity:city us
```

編寫命令

```php
public function handle()
{
    try {
       echo "Hello World";
    } catch (QueryException $ex) {
        dd($ex->getMessage());
    }
}
```

### 參數

- 花括號中為傳入的參數

  - {country_code?} 可選的參數
  - {country_code=tw} 參數的預設值
  - {country_code*} `*`代表輸入的是陣列
  - {country_code:國家代碼} 加入敘述

- 選項：關鍵字是`--`

  - {--quene=} 選項後面加上 `=`，表示選項需要明確指定值。
  - {--Q|quene} 可用簡寫
  - {--id=_} `_`代表輸入的是陣列
  - {--quene=:這個工作是否該進入隊列} 加入敘述

- 取得參數

  - $this->argument('user') 取得單一 user 參數
  - $this->arguments() 取得所有參數，以陣列呈現
  - $this->options('quene') 取得單一 quene 選項
  - $this->options() 取得所有選項，以陣列呈現

- 互動式指令

  - ask 詢問

    ```php
    $name = $this->ask('what is ur name');
    ```

  - secret 詢問，但回答在終端機看不到

    ```php
    $name = $this->secret('what is ur password');
    ```

  - confirm 確認問題，預設回傳值是 false，若使用者回傳 y 則回傳 true

    ```php
    if ($this->confirm('wanna continue?')) {
        //
    }

    ```

  - anticipate 自動完成，若是 Taylor 或是 Dayle 這兩個選項就自動完成，使用者亦可以輸入其他選項

    ```php
    $name = $this->anticipate('what is ur name', ['Taylor', 'Dayle']);
    ```

  - choice 選項選擇，使用者輸入 key 值

    ```php
    $name $this->choice('what is ur name', ['Sunny', 'Taylor', 'Dayle'], $defaultIndex);
    ```

    ```log
    What is your name? [Taylor]: //在這個$defaultIndex = 1
      [0] Sunny
      [1] Taylor
      [2] Dayle
     > 0

    Display name: Sunny
    ```

### 註冊 command

app\console\Commands\Kernel.php

```php
protected $commands = [
    EditCity::class
];
```
