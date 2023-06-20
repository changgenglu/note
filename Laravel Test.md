# Laravel Test

> Laravel 預設支援 PHPUnit 來進行測試
>
> 設定文件 phpunit.xml
>
> 在 test 資料夾中有兩個子資料夾
>
> Feature 功能測試是針對大面積的程式碼進行測試
>
> Unit 單元測試是針對單一方法單獨進行測試

## 啟動測試

建立測試文件

```bash
// 在 feature 資料夾下建立一個測試的 class
php artisan make:test UserTest

// 在 unit 資料夾底下鍵立一個測試 class
php artisan make:test UserTest --unit
```

```php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function testBasicTest()
    {
        $this->assertTrue(true);
    }
}
```

啟動測試

```bash
php artisan test

// 指定要運行的特定測試類別
php artisan test --filter ExampleTest

// 運定特定的測試方法
php artisan test --filter ExampleTest::testExample

// 傳遞參數
php artisan test --testsuite=Feature --stop-on-failure
```
