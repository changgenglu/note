# Laravel 實作權限

## Gates

### 使用者角色

將使用者區分為:

- 系統管理者: admin
- 一般管理者: manager
- 一般使用者: user

編輯`app/User.php`，加入帳號角色名稱常數，並將`role`欄位加入 `fillable` 中

```php
class User extends Authenticatable
{
    // ...

    const ROLE_ADMIN = 'admin';
    const ROLE_MANAGER = 'manager';
    const ROLE_USER = 'user';

    protected $fillable = [
        'name', 'email', 'password', 'role',
    ];

    // ...
}
```

### 資料庫 migration

建立一個 `migration` 設定檔，在 `users` 資料表中加入儲存帳號角色的 `role` 欄位

```php
class AddRoleColumnToUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('role')->default(Admin::ROLE_USER);
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('role');
        });
    }
}
```

修改完成後，執行 migrate

### 使用者註冊 Controller

編輯`app/Http/Controllers/Auth/RegisterController.php`，設定使用者註冊時，預設角色為一般使用者

```php
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

### 建立 Gate 規則權限

編輯`app/Providers/AuthServiceProvider.php`，加入自訂的 Gates 規則

```php
use App\User;

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

### 在 Blade 運用 Gate 權限設定

在 Blade 樣板中可以運用 `@can`、`@cannot` 或 `@canany` 來判斷使用者的權限

```php
@can('admin')
    <!-- 系統管理者 -->
@elsecan('manager')
    <!-- 一般管理者 -->
@else
    <!-- 一般使用者 -->
@endcan
```

### Controller 運用 Gate 權限設定

在 Controller 中則可使用 `Gate::allows` 或 `Gate::denies` 判斷使用者權限

```php
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

運用 `authorize` 直接限制整個函數的執行權限

```php
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

### Middleware 運用 Gate 權限設定

應用在 Route

```php
// 只有系統管理者可以執行
Route::get('/someAction', 'MyController@someAction') -> middleware('can:admin');
```

## Policy

可以針對一個 Model 或資源實作限制權限

```php
php artisan make:policy PostPolicy --model=Post
```

到`app/Providers/AuthServiceProvider`註冊剛建立好的 Policy

```php
protected $policies = [
    // 'App\Model' => 'App\Policies\ModelPolicy',
    Post::class => PostPolicy::class,
];
```

到`app/Policies/PostPolicy`修改條件

```php
public function update(User $user, Post $post)
{
    return $user->id === $post->user_id;
}
```
