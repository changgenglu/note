# Laravel Migration & Seeder & Factory

###### tags: `php` `Laravel`

## Migration

### 產生 Migration 檔案

```cmd
php artisan make:migration create_your_table
```

- 產生出來的 Migration 檔案，內有 up() 和 down() 兩個方法。

```php
    public function up()
    {
        Schema::create('room_user', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('user_id');
            $table->bigInteger('parent_id')->comment('將此成員加入系統的使用者');
            $table->unsignedBigInteger('room_id')->nullable();
            $table->bigInteger('role_id')->comment('使用者身分');
            $table->timestamps();
        });

        Schema::table('room_user', function (Blueprint $table) {
            $table->foreign('user_id')
                ->references('id')
                ->on('users')
                ->onDelete('cascade');
            $table->foreign('room_id')
                ->references('id')
                ->on('rooms')
                ->nullOnDelete();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('model_has_user');
    }
```

### 執行 Migrate

```cmd
php artisan migrate
```

### 還原 Migrate

- 實際上他會實作 down() ，並將資料表內的資料刪掉。

```cmd
php artisan migrate:rollback
php artisan migrate:rollback --step=5 // 還原最後 5 個 Migration
```

- 如果今天將 down() 註解掉，一樣會進行 Migrate 刪除 table 中的資料，但不會刪除資料表。

### 重置 Migration

```cmd
//重置所有 Migration
php artisan migrate:refresh

//重置所有 Migration 並同時建立資料
php artisan migrate:refresh --seed
```

## [Seed](https://ithelp.ithome.com.tw/articles/10216376)

### 產生 Seeder 檔案

```cmd
php artisan make:seed PersonTableSeeder
```

此時會多出一個檔案`database/seeds/PersonTableSeeder.php`，
在`database/seeds/DatabaseSeeder.php`中，修改要呼叫的 Seeder：

```php=
$this->call(PersonTableSeeder::class);
```

執行 Seeder 時會呼叫 seeder 類別裡預設的 run() 方法。

在`PersonTableSeeder.php`中加入要產生的資料。

```php=
public function run()
    {
        DB::table('users')->insert([
            'last_name' => str_random(10),
            'email' => str_random(10).'@gmail.com',
            'password' => bcrypt('secret'),
        ]);
    }
```

也可以使用模型工廠 factory 來大量生產資料。

```php=
factory(App\Person::class, 50)->create();
// 使用 Person 的 factory
```

### 執行 Seeder

用`--class`來指定特定的 Seeder Class

```cmd
php artisan db:seed

php artisan db:seed --class=UsersTableSeeder
```

## [Factory 翻譯官方文件](https://learnku.com/docs/laravel/6.x/database-testing/5185)

[深入了解 Facker](https://learnku.com/laravel/t/62386)

### 產生 Factory 檔案

```cmd
php artisan make:factory PersonFactory --model=Person
```

`--model=Person`選項用在指定 Factory 創建的 model 名稱，將會為指定 model 產生 Factory 文件。

到`/database/factories/PersonFactory.php`，設定要填充的資料欄位。

```php=
$factory->define(Person::class, function (Facker $facker) {
    return [
        'last_name' => $faker->lastName,
        'email'     => $faker->safeEmail,
        'password'  => $faker->password
    ];
});
```

### [Faker Formatters](https://github.com/fzaninotto/Faker#formatters)

- Faker 使用中文

在 config\app.php 下新增一行，指定 Faker 使用 zh_TW 語言

```php=
'faker_locale' => 'zh_TW',
```
