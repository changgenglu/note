# SOLID 物件導向原則

> 參考資料
>
> [物件導向設計原則 SOLID](https://clouding.city/php/solid/)

## SPR 單一職責原則

> Single Responsibility Principle

### 定義

應該且僅有一個原因引起類別的變更，讓類別只有一個職責。

### 秘訣

- 關注點分離
- 不應該因為貪圖方便塞在一起
- 若切分太細，會有類別太多的問題

### 提醒

- 設計階段就可以避開類別職責太大的問題
- 在維護階段需小心別又讓類別職責變多

Bad

```php
class UserSettings
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function changSettings(array $settings): void
    {
        if ($this->verifyCredentials()) {
            //
        }
    }

    private function verifyCredentials(): bool
    {
        //
    }
}
```

Good

```php
class UserAuth
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function verifyCredentials(): bool
    {
        //
    }
}

class UserSetters
{
    private $user;

    private $auth;

    public function __construct(User $user)
    {
        $this->user = $user;
        $this->auth = new UserAuth($user);
    }

    public function ChangSettings(array $settings): void
    {
        if ($this->auth->verifyCredentials()) {
            //
        }
    }
}
```

## Open Closed Principle 開放封閉原則

### 定義

軟體中的對象(類別、函數)，對於擴展是開放的，對於修改是封閉的。

### 秘訣

- 只考慮抽象層級的介面互動
- 把變化委託給其他類別處理
- 只異動 metadata 或 config

### 提醒

- 不是所有程式都遵守 OCP
- 可能一開始無法預想到要擴充，但可以透過重構完成
- 不要過度用繼承的方式來進行擴充

Bad

```php
abstract class Adapter
{
    protected $name;

    public function getName(): string
    {
        return $this->name;
    }
}

class AjaxAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'ajaxAdapter';
    }
}

class NodeAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'nodeAdapter';
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): promise
    {
        $adapterName = $this->adapter->getName();

        if ($adapterName === 'ajaxAdapter') {
            return $this->makeAjaxCall($url);
        } elseif ($adapterName === 'httpNodeAdapter') {
            return $this->makeHttpCall($url);
        }
    }

    private function makeAjaxCall(string $url): promise
    {
        // request and return promise
    }

    private function makeHttpCall(string $url): promise
    {
        // request and return promise
    }
}
```

Good

```php
interface Adapter
{
    public function request(string $url): promise;
}

class AjaxAdapter implements Adapter
{
    public function request(string $url): promise
    {
        // request and return promise
    }
}

class NodeAdapter implements Adapter
{
    public function request(string $url): promise
    {
        // request and return promise
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): promise
    {
        return $this->adapter->request($url);
    }
}
```

## Liskov Substitution Principle 里氏替換原則

### 定義
