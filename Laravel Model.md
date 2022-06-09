# Laravel Model

建立 Model

```php
php artisan make:model New
```

建立 Model 同時建立 migration controller

```php
php artisan make:model New -mcr
```

## Model 基本屬性

```php
class UserInfo extends Model
{
    protected $table = 'user_data';  // 資料表名稱
    protected $primaryKey = 'id';   // 主鍵
    public $timestamps = false;
    protected $fillable = [
        'userId',
        'userName',
        'account',
        'passord',
        'email'
    ];
}
```

- public $timestamps = false // 設定時間戳記
- $fillable ：調用 create() update() 時，可以大量新增、修改的欄位。若沒有添加這個屬性，新增修改的動作將無法實現。

  - fillable：設定可以大量新增的欄位（白名單）

    ```php=
    class User extends Model {
        protected $fillable = ['userId','userName','account','pw','email'];
    }
    ```

  - guarded：設定需要被保護的欄位（黑名單）

    ```php=
    class User extends Model {
        protected $guarded = [‘uuid’, ‘pw’];
    }
    ```

## [多型態關聯](https://blog.epoch.tw/2020/04/30/%E5%9C%A8-Laravel-7-0-%E4%BD%BF%E7%94%A8-Eloquent-%E5%A4%9A%E5%9E%8B%E9%97%9C%E8%81%AF/)

> 多型態關聯可以讓一張表同時關連到兩張以上的資料表
>
> 優點:
> 可以合一管理資類類似的資料結構與處理邏輯
> 缺點:
> 耦合提高，變動邏輯時容易影響到某一方的操作

假設我們有 user 跟 post 兩種資料，而他們各自有所屬的 image 資料表

| users |
| :---: |
|  id   |
| name  |

| user_images |
| :---------: |
|     id      |
|   user_id   |
|     url     |

| posts |
| :---: |
|  id   |
| name  |

| post_images |
| :---------: |
|     id      |
|   post_id   |
|     url     |

如果 image 的資料結構和處理邏輯相似，就可以使用多型態關聯，將表單合成一張

| users |
| :---: |
|  id   |
| name  |

| posts |
| :---: |
|  id   |
| name  |

|     images     |
| :------------: |
|       id       |
| imageable_type |
|  imageable_id  |
|      url       |

- imageable_id : 關聯的主鍵值
- imageable_type : 指定這筆資料是關聯 users 資料表還是 posts 資料表，欄內儲存的是類別名稱，型態為字串，如：'App\Models\User' 'App\Models\Post'

### 一對一關聯

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Image extends Model
{
    public function imageable()
    {
        return $this->morphTo();
    }
}

class Post extends Model
{

    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}

class User extends Model
{

    public function image()
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}
```

#### morphOne: hasOne 的多型態版本

和 hasOne 相比，標類別後面多了一個 name 參數 imageable，Eloquent 會根據這個 name 預設目標表單上的查詢欄位，以 imageable 為例的話就是查詢 imageable_type 跟 imageable_id

- 自訂目標的查詢欄位名稱

  ```php
  // morphOne(目標表單名稱，多形名稱，目標的型別欄位名稱，目標的外鍵欄位名稱，自己的關聯鍵)
   $this->morphOne(Image::class, 'imageable','imageable_type',  'imageable_id','id');
  ```

#### morphTo: belongsTo 的多型態版本

```php
// 使用時要注意函式的名稱
public function imageable()
{
    return $this->morphTo();
}
```

如果沒有帶入參數的話預設會用函式的名稱產出預設名稱，像這裡的函式名稱是 imageable ，那查詢時就會以型別欄位 imageable_type 查對應的資料表，以及鍵值欄位 imageable_id 查資料。

- 自訂預設名稱

  ```php
  $this->morphTo('imageable');
  ```

- 自訂查詢欄位的名稱

  ```php
  // morphOne(多形名稱，型別欄位名稱，外鍵欄位名稱)
  $this->morphTo('imageable'，'imageable_type','imageable_id');
  ```

### 一對多關聯

和一對一的關聯差不多，差別在 morphOne 改成 morphMany，查詢得到的資料不是一筆而是一組

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Image extends Model
{
    public function imageable()
    {
        return $this->morphTo();
    }
}

class Post extends Model
{

    public function image()
    {
        return $this->morphMany(Image::class, 'imageable');
    }
}

class User extends Model
{

    public function image()
    {
        return $this->morphMany(Image::class, 'imageable');
    }
}
```

### 多對多關聯

> 和普通多對多關聯相比，中介表為多型態

影片和文章有相同的 tag，一篇文章和影片同時會有多個 tag，一個 tag 也會同時關連到多個影片或文章
| videos |
| :----: |
| id |
| name |

| posts |
| :---: |
|  id   |
| name  |

|   taggables   |
| :-----------: |
|      id       |
|    tag_id     |
| taggable_type |
|  taggable_id  |

| tags |
| :--: |
|  id  |
| name |

多型的一方，會用 morphToMany 方法

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Video extends Model
{
    /**
     * Get all of the tags for the post.
     */
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Post extends Model
{
    /**
     * Get all of the tags for the post.
     */
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

反向關聯會使用 morphedByMany 方法

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tag extends Model
{
    /**
     * Get all of the posts that are assigned this tag.
     */
    public function posts()
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }

    /**
     * Get all of the videos that are assigned this tag.
     */
    public function videos()
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }
}
```

#### morphToMany: belongsToMany 的多型態版

需要帶多型的名稱參數才能查詢資料

```php
// morphOne(目標表單名稱，多形名稱)
 $this->morphToMany(Tag::class, 'taggable');
```

- 自訂名稱

  ```php
  // morphToMany(目標表單名稱，多形名稱，中介表單名稱，中介表單上參照自己的外鍵，中介表單上參照目標的外鍵，目標的關聯鍵，自己的關聯鍵)
  $this->morphToMany(Tag::class, 'taggable','taggables',  'taggable_id','tag_id','id','id');
  ```

#### morphedByMany: 多對多的反向多型關聯

- 自訂名稱

  ```php
  // morphedByMany(目標表單名稱，多形名稱，中介表單名稱，中介表單上參照目標的外鍵，中介表單上參照自己的外鍵，自己的關聯鍵，目標的關聯鍵)
  $this->morphedByMany(
      Post::class,  // 目標表單名稱
      'taggable',   // 多型名稱
      'taggables',  // 中介表明稱
      'taggable_id',// 中介表單上參照目標的外鍵
      'tag_id',     // 中介表單上參照自己的外鍵
      'id',         // 自己的關聯鍵
      'id'          // 目標的關聯鍵
    );
  ```

## Laravel ORM 將資料存至資料庫

### save

將表格第一筆資料的名字，改成小華

```php
User::find(1)->save(['name' => '小華']);
```

也可以一次編輯多個欄位

```php
User::find(1)->save([
  'name'=>'小華',
  'email'=>'flower@gmail.com'
  'sex'=>'male'
]);
```

### 多對多關聯時

- articles

| id  | article_name |
| :-: | :----------: |
|  1  |   article1   |
|  2  |   article2   |
|  3  |   article3   |
|  4  |   article5   |

- tags

| id  | tag_name |
| :-: | :------: |
|  1  |   tech   |
|  2  |  music   |
|  3  |   art    |
|  4  |   food   |

- article_tag

| article_id | tag_id |
| :--------: | :----: |
|     1      |   2    |
|     1      |   3    |
|     1      |   4    |
|     3      |   4    |

建立和控制多對多關係的方法

- 建立:

  - attach()

  ```php
  // id=3 的文章原有的標籤[4]
  Article::find(3)->tags()->attach([1,2]);
  // id=3 的文章加入標籤[1, 2]
  // id=3 的文章的標籤有[1, 2, 4]
  ```

  - sync()

  ```php
    Article::find(3)->tags()->sync([1,2]);
    // 文章3會有三個標籤：1,2
    // 不在傳入的陣列中的值，將會被從中介表中刪除
  ```

  - save()

  ```php
  Article::find(3)->tags()->saveMany([
      Tag::find(1),
  Tag::find(2)
  ]);
  // save輸入的類型須為model
  // 多個時要用saveMany
  ```

  |   方法   | 單個 id | 多個 id | 單個 model |   多個 model    |
  | :------: | :-----: | :-----: | :--------: | :-------------: |
  | attach() |    V    |    V    |     V      |                 |
  |  sync()  |    V    |    V    |     V      |                 |
  |  save()  |         |         |     V      | 要用 saveMany() |

- 刪去: detach()

```php
$article = Article::find(1);

// 從文章上移除指定tag
$article->tags()->detach($tagid);

// 移除文章所有tag
$article->tags()->detach();
```

- 切換: toggle()

```php
$article->tags()->toggle([1, 2 ,3]);
// 用來切換傳入id的附加狀態，如果傳入的id目前已經被附加，他將會被卸除。
// 若已經被卸除，將會被附加
```
