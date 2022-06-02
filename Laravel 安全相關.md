# [Laravel 安全相關](https://learnku.com/docs/laravel/6.x/authentication/5151)

###### tags: `php` `Laravel`

## [用戶認證](https://learnku.com/docs/laravel/6.x/authentication/5151)

```cmd
composer require laravel/ui "^1.0" --dev

php artisan ui vue --auth
```

## [用戶權限](https://laravel.com/docs/6.x/authorization#via-blade-templates)

使用 Gates 實作權限管理

### 建立使用者角色

編輯`app/Models/User.php` 加入三種帳號角色名稱的常數字串，並將`role` 這個欄位加入`fillable` 中

```php=
class User extends Authenticatable
{
    // ...

    public const ROLE_ADMIN = 'admin';
    public const ROLE_MANAGER = 'manager';
    public const ROLE_USER = 'user';

    protected $fillable = [
        'name', 'email', 'password', 'role',
    ];

    // ...
}
```

### 資料庫 migration

編輯 migration 設定檔，在其中加入儲存帳號角色的`role` 欄位

```php=
use App\User;

class CreateUsersTable extends Migration
{
    // ...

    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->string('role')->default(User::ROLE_USER); // 加入角色欄位
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
}
```

### 使用者註冊 Controller

在註冊新使用者時，預設權限為一般使用者

```php=
protected function create(array $data)
{
    return User::create([
        'name' => $data['name'],
        'email' => $data['email'],
        'password' => Hash::make($data['password']),
        'role' => User::ROLE_USER,  // 預設為一般使用者
    ]);
}
```

### 建立 Gate 權限規則

在`app/Providers/AuthServiceProvider.php`，加入自訂的 Gates 規則。

```php=
use App\Models\User;

class AuthServiceProvider extends ServiceProvider
{
    // ..

    public function boot()
    {
        $this->registerPolicies();

        // 系統管理者 Gate 規則
        Gate::define('admin', function ($user) {
            return $user->role === User::ROLE_ADMIN;
        });

        // 一般管理者 Gate 規則
        Gate::define('manager', function ($user) {
            return $user->role === User::ROLE_MANAGER;
        });

        // 一般使用者 Gate 規則
        Gate::define('user', function ($user) {
            return $user->role === User::ROLE_USER;
        });
    }
}
```

### 運用 Gate 權限設定

#### blade 樣板

運用`@can` `@cannot` `@canany`來判斷使用者權限

```php=
@can('admin')
    <!-- 系統管理者 -->
@elsecan('manager')
    <!-- 一般管理者 -->
@else
    <!-- 一般使用者 -->
@endcan
```

#### Controller 運用

運用`Gate::allows` `Gate::denies`

```php=
use Illuminate\Support\Facades\Gate;

class Controller extends BaseController
{
    // ...

    public function someAction()
    {
        if (Gate::allows('admin')) {
            return '系統管理者。';
        }

        if (Gate::denies('admin')) {
            return '非系統管理者！';
        }
    }
}
```

另外也可以使用`authorize()` 直接限制整個函數的執行權限

```php=
class Controller extends BaseController
{
    // ...

    // 只有系統管理者可以執行
    public function adminAction()
    {
        $this->authorize('admin');

        // ...
    }
}
```

- [使用 authorizeResource 簡化 Resource Controller 用戶授權](https://learnku.com/articles/4707/use-authorizeresource-to-simplify-your-resource-controller-user-license)

只要在 controller 的\_\_construct() 裡面加上 authorizeResource()。在 controller 函式的邏輯運行前，都會自動進行權限判斷。

#### Middleware 權限設定

在 Route 之中，用 Middleware 設定 Gate

```php=
// 只有系統管理者可以執行
Route::get('/someAction', 'MyController@someAction') -> middleware('can:admin');
```
