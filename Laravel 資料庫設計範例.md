# Laravel 資料庫設計範例

###### tags: `php` `Laravel`

### 建立新專案

```cmd
laravel new blog
```

### 編輯 .env

```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_name
DB_PASSWORD=your_password
```

- XAMPP 預設的密碼是空白，phpEnv 要先行設定資料庫密碼

### 同時建立 migration controller model

終端機執行

```cmd
php artisan make:model Employee -mcr
```

完成後產生三個檔案

- database\migrations\yyyy_mm_dd_time_create_employees_table.php
- app\Http\Controllers\EmployeeController.php
- app\Models\Employee.php

修改 migration 檔案，將 up() 函式改成

```php
public function up()
{
    Schema::create('employees', function (Blueprint $table) {
        $table->bigIncrements('id');
        $table->string('firstName');
        $table->string('lastName');
        $table->timestamps();
    });
}
```

執行 migrate ，在資料庫中建立資料表

```cmd
php artisan migrate
```

### 建立資料

終端機開啟 tinker 工具程式

```cmd
php artisan tinker
```

依序輸入指令

```cmd
1 + 2
DB::table('employees')->insert(['firstName' => 'Jeremy', 'lastName' => 'Lin', 'created_at' => new Datetime, 'updated_at' => new Datetime ])
DB::table('employees')->insert(['firstName' => 'Derek', 'lastName' => 'Jeter', 'created_at' => new Datetime, 'updated_at' => new Datetime ])
DB::table('employees')->insert(['firstName' => 'Lionel', 'lastName' => 'Messi', 'created_at' => new Datetime, 'updated_at' => new Datetime ])
DB::table('employees')->insert(['firstName' => 'test', 'lastName' => 'test', 'created_at' => new Datetime, 'updated_at' => new Datetime ])

DB::table('employees')->get();
DB::table('employees')->find(1)
DB::table('employees')->where('lastName', 'test')->first()
DB::table('employees')->where('lastName', 'test')->delete()

App\Models\Employee::all();
App\Models\Employee::find(1);

exit
```

### 渲染員工清單

編輯 web.php

```php
Route::get('/', 'App\Http\Controllers\EmployeeController@index');
Route::resource('employees', 'App\Http\Controllers\EmployeeController');
```

編輯 EmployeesController，需參用 Employee 類別

```php
use App\Models\Employee;
```

修改 index()

```php
public function index() {
        $employeeList = Employee::all();
        return view('employees.index', compact('employeeList'));
    }
```

建立 resources\views\employees\index.blade.php

```php
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Employee</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/js/bootstrap.min.js"></script>
</head>
<body>

<div class="container">
  <h2>Employee List
    <a href="/employees/create" class="btn btn-md btn-success pull-right">
      <span class="glyphicon glyphicon-plus"></span> 新增
    </a>
  </h2>
  <table class="table table-hover">
    <thead>
      <tr>
        <th>Firstname</th>
        <th>Lastname</th>
        <th>&nbsp;</th>
      </tr>
    </thead>
    <tbody>
    <tr>
        <td>firstName1</td>
        <td>lastName1</td>
        <td>
            <span class="pull-right">
                <form method="post" action="/employees/1">
                    <a href="/employees/1/edit" class="btn btn-xs btn-info">
               <span class="glyphicon glyphicon-pencil"></span> 修改
              </a> |
                    @csrf
                    @method('DELETE')
                    <button type="submit" class="btn btn-xs btn-danger">
               <span class="glyphicon glyphicon-remove"></span> 刪除
              </button>
                </form>
            </span>
        </td>
    </tr>
    <tr>
        <td>firstName2</td>
        <td>lastName2</td>
        <td>
            <span class="pull-right">
                <form method="post" action="/employees/2">
                    <a href="/employees/2/edit" class="btn btn-xs btn-info">
               <span class="glyphicon glyphicon-pencil"></span> 修改
              </a> |
                    @csrf
                    @method('DELETE')
                    <button type="submit" class="btn btn-xs btn-danger">
               <span class="glyphicon glyphicon-remove"></span> 刪除
              </button>
                </form>
            </span>
        </td>
    </tr>
    <tr>
        <td>firstName3</td>
        <td>lastName3</td>
        <td>
            <span class="pull-right">
                <form method="post" action="/employees/3">
                    <a href="/employees/3/edit" class="btn btn-xs btn-info">
               <span class="glyphicon glyphicon-pencil"></span> 修改
              </a> |
                    @csrf
                    @method('DELETE')
                    <button type="submit" class="btn btn-xs btn-danger">
               <span class="glyphicon glyphicon-remove"></span> 刪除
              </button>
                </form>
            </span>
        </td>
    </tr>
    </tbody>
  </table>
</div>
</body>
</html>
```

啟動 Web 伺服器

```cmd
php artisan serve
```

### 動態渲染員工資料

修改 resources\views\employees\index.blade.php

```php
<tbody>
    @foreach ($employeeList as $emp)
     <tr>
         <td>{{$emp->firstName}}</td>
         <td>{{$emp->lastName}}</td>
         <td>
             <span class="pull-right">
                 <form method="post" action="/employees/{{$emp->id}}">
                     <a href="/employees/{{$emp->id}}/edit" class="btn btn-xs btn-info">
          <span class="glyphicon glyphicon-pencil"></span> 修改
         </a> |
                     @csrf
                     @method('DELETE')
                     <button type="submit" class="btn btn-xs btn-danger">
          <span class="glyphicon glyphicon-remove"></span> 刪除
         </button>
                  </form>
             </span>
         </td>
     </tr>
    @endforeach
</tbody>
```

### 新增員工資料

修改 EmployeesController.php

```php
public function create()
    {
        return view("employees.create");
    }
```

新增檔案 resources\views\employees\create.blade.php

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <title>Employee</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/js/bootstrap.min.js"></script>
</head>

<body>
    <div class="container">
        <form class="form-horizontal">
     @csrf
            <fieldset>
                <!-- Form Name -->
                <legend>Employee Data</legend>
                <!-- Text input-->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="firstName">First Name:</label>
                    <div class="col-md-4">
                        <input id="firstName" name="firstName" type="text" placeholder="" class="form-control input-md">
                    </div>
                </div>
                <!-- Text input-->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="lastName">Last Name:</label>
                    <div class="col-md-4">
                        <input id="lastName" name="lastName" type="text" placeholder="" class="form-control input-md">
                    </div>
                </div>
                <!-- Button (Double) -->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="okOrCancel"></label>
                    <div class="col-md-8">
                        <button type="submit" id="okOrCancel" name="okOrCancel" class="btn btn-success">
                         OK
                        </button>
         <button type="submit" id="okOrCancel" name="okOrCancel" class="btn btn-danger">
                         Cancel
                        </button>
                    </div>
                </div>
            </fieldset>
        </form>
    </div>
</body>
</html>
```

修改 EmployeesController.php

```php
public function store(Request $request)
    {
        $emp = new Employee();
        $emp->firstName = $request->firstName;
        $emp->lastName = $request->lastName;
        $emp->save();
        return redirect("/employees");
    }
```

### 修改會員資料

修改 EmployeesController.php

```php
public function edit($id)
    {
        $emp = Employee::find($id);
        return view('employees.edit', compact('emp'));
    }
```

新增檔案 resources\views\employees\edit.blade.php

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <title>Employee</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.0/js/bootstrap.min.js"></script>
</head>

<body>
    <div class="container">
        <form class="form-horizontal">
     @csrf
  @method('PUT')
            <fieldset>
                <!-- Form Name -->
                <legend>Employee Data</legend>
                <!-- Text input-->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="firstName">First Name:</label>
                    <div class="col-md-4">
                        <input id="firstName" name="firstName" value="{{ $emp->firstName }}" type="text" placeholder="" class="form-control input-md">
                    </div>
                </div>
                <!-- Text input-->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="lastName">Last Name:</label>
                    <div class="col-md-4">
                        <input id="firstName" name="firstName" value="{{ $emp->lastName }}" type="text" placeholder="" class="form-control input-md">
                    </div>
                </div>
                <!-- Button (Double) -->
                <div class="form-group">
                    <label class="col-md-4 control-label" for="okOrCancel"></label>
                    <div class="col-md-8">
                     <button type="submit" id="okOrCancel" name="okOrCancel" class="btn btn-success">
                         OK
                        </button>
         <button type="submit" id="okOrCancel" name="okOrCancel" class="btn btn-danger">
                         Cancel
                        </button>
                    </div>
                </div>
            </fieldset>
        </form>
    </div>
</body>
</html>
```

修改 EmployeesController.php

```php
public function update(Request $request, $id)
    {
        $emp = Employee::find($id);
        $emp->firstName = $request->firstName;
        $emp->lastName = $request->lastName;
        $emp->save();
        return redirect("/employees");
    }
```

### 刪除會員資料

修改 EmployeesController.php

```php
public function destroy($id)
    {
        $emp = Employee::find($id);
        $emp->delete();
        return redirect("/employees");
    }
```
