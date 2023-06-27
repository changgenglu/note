# Laravel 環境設置

- [Laravel 環境設置](#laravel-環境設置)
  - [環境初始設定](#環境初始設定)
    - [1. 安裝 XAMPP or phpEnv](#1-安裝-xampp-or-phpenv)
      - [xampp 更改 php 版本 (版本 5 =\> 7)](#xampp-更改-php-版本-版本-5--7)
      - [macOS Monterey 上安裝 PHP](#macos-monterey-上安裝-php)
    - [2. 安裝 composer](#2-安裝-composer)
      - [windows 透過 composer 官網下載 composer 安裝檔](#windows-透過-composer-官網下載-composer-安裝檔)
      - [下載 Composer(MacOS)](#下載-composermacos)
      - [全局調用 Composer (MacOS)](#全局調用-composer-macos)
    - [3. 安裝 Visual Studio Code or phpStorm](#3-安裝-visual-studio-code-or-phpstorm)
    - [4. Laravel 全域安裝 (XAMPP)](#4-laravel-全域安裝-xampp)
  - [從 Git clone Laravel 專案](#從-git-clone-laravel-專案)
    - [開發環境設定](#開發環境設定)
    - [上線環境設定](#上線環境設定)
    - [composer install 失敗](#composer-install-失敗)
  - [Laravel ReactJS](#laravel-reactjs)
  - [Laravel 安裝 bootstrap](#laravel-安裝-bootstrap)
    - [Laravel 8](#laravel-8)
    - [Laravel 6](#laravel-6)

## 環境初始設定

### 1. 安裝 XAMPP or phpEnv

#### xampp 更改 php 版本 (版本 5 => 7)

> 注意！php 8.1 不相容 laravel 6.x 以下(包含 6)

1. 開啟 Apache Admin 查看當前 XAMPP 所有版本資訊
2. 到[XAMPP](https://windows.php.net/download/)下載要更新的 php 版本的 zip 檔。(注意！選擇 `Thread Safe` 版本！)
3. 解壓縮定指定資料夾名稱為`php`，將此資料夾放至 XAMPP 資料夾中，並將原本的 php 資料夾另外命名
4. 至 XAMPP 控制面板點選 `config` 按鈕，開啟 `httpd-xampp.conf` 檔，並修改其內容

   1. 找到以下文字，並將其修改

      修改前

      ```txt
      LoadFile "C:/xampp/php/php5ts.dll"
      LoadFile "C:/xampp/php/libpq.dll"
      LoadModule php5_module "C:/xampp/php/php5apache2_4.dll"
      ```

      修改後

      ```txt
      LoadFile "C:/xampp/php/php7ts.dll"
      LoadFile "C:/xampp/php/libpq.dll"
      LoadModule php7_module "C:/xampp/php/php7apache2_4.dll"
      ```

      - 修改時需確認修改路徑的檔案確實存在，若無此檔案，可能是 php 版本的關係

   2. 將 `httpd-xampp.conf` 設定檔中所有 `php5_module` 改為 `php7_module`
      - 在 php8 的 `httpd-xampp.conf` 設定檔為 `php_module`

5. 重建 `php.ini` 設定檔

   1. 複製 php 資料夾中的 php.ini-development，並重新命名為 php.ini
   2. 開啟 php.ini 並依開發或網站需求，開啟相關模組(刪除前面的分號`;`)
      1. `Dynamic Extensions` 動態延伸功能
         - extension=curl
         - extension=gd2(version 7) / gd(version 8)
           - 在 php 8.0，DG 延伸功能 windows dll 文件名稱由 php_gd2.dll 改為 php_gd.dll)
         - extension=mbstring
         - **extension=mysqli**
         - extension=openssl
      2. `Paths and Directories` 路徑和目錄
         - **extension_dir = "ext"**
      3. 常見設定
         - max_execution_time = 600
         - short_open_tag = On
         - max_input_time = 180
         - **error_reporting=E_ALL & ~E_DEPRECATED & ~E_STRICT**
           - 設置錯誤訊息通知，加入版本兼融性的提示
         - memory_limit = 500M
         - post_max_size = 500M
         - upload_max_filesize = 100M
         - max_file_uploads = 50

6. 至 XAMPP 面板重啟 Apache
7. 重新執行 composer update

#### macOS Monterey 上安裝 PHP

> 問題：安裝完 MAMP 之後，要用終端機安裝 composer，結果出現`zsh: command not found: php`
>
> 原因：MacOS Monterey 版本，預設沒有安裝 PHP。

1. 安裝 PHP
   [Installing PHP on your Mac](https://daily-dev-tips.com/posts/installing-php-on-your-mac/)

1. 安裝 Homebrew
   在 terminal 輸入

   ```terminal
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   顯示路徑問題的解決辦法

   ```terminal
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/changgenglu/.zprofile

   eval "$(/opt/homebrew/bin/brew shellenv)"
   ```

1. 使用 Homebrew 安裝 PHP
   先確定 Homebrew 安裝成功

   ```terminal
   brew update
   brew doctor
   ```

   安裝 PHP

   ```terminal
   brew install php
   ```

   安裝特定版本

   ```terminal
   brew install php@7.4
   ```

   - 安裝指定版本後，並不會自動切換 PHP 本版本

1. 使用 Homebrew 切換 PHP
   檢查當前版本

   ```terminal
   php -v

   # PHP 8.0.1 (cli) (built: Jan  8 2021 01:27:28) ( NTS )
   ```

   取消該版本

   ```terminal
   brew unlink php@8.0
   ```

   選擇版本

   ```terminal
   brew link php@7.4
   ```

   出現路徑問題，提示：須遜行腳本來添加路徑

   ```terminal
   echo 'export PATH="/opt/homebrew/opt/php@7.4/bin:$PATH"' >> ~/.zshrc

   ```

### 2. 安裝 composer

#### windows 透過 composer 官網下載 composer 安裝檔

#### 下載 Composer(MacOS)

- 代碼以[Composer 官網](https://getcomposer.org/download/)為主

下載安裝程序到當前目錄

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

驗證安裝程序

```bash
php -r "if (hash_file('sha384', 'composer-setup.php') === '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```

運行安裝程序

```bash
php composer-setup.php
```

刪除安裝程序

```bash
php -r "unlink('composer-setup.php');"
```

- MacOS 如果出現 `zsh: command not found: php`

  原因：MacOS Monterry 版本，沒有包括 PHP。請參考：[macOS Monterey 上安裝 PHP](https://hackmd.io/wnFCr0GUS-iIRxHY2zrBgw)

- MacOS 須確保 Composer 的系統等級 vendor bin 資料夾有放在$PATH 中，這樣作業系統才能找到`laravel` 可執行檔。一般常見的位置如下：
  - macOS: $HOME/.composer/vendor/bin
  - Windows: %USERPROFILE%\AppData\Roaming\Composer\vendor\bin

#### 全局調用 Composer (MacOS)

確認是否成功安裝 Composer

```bash
ls

// 要看到有composer.phar的檔案
```

將 composer.phar 放入本地的目錄

```bash
sudo mv composer.phar /usr/local/bin/composer
```

測試是否安裝成功

```bash
composer
```

### 3. 安裝 Visual Studio Code or phpStorm

### 4. Laravel 全域安裝 (XAMPP)

```bash
cd c:\xampp\htdocs
composer global require laravel/installer

laravel new project_name

cd project_name

php artisan serve
```

## 從 Git clone Laravel 專案

由於安全性及維護的考量，Laravel 預設有 .gitignore，所以較為敏感的檔案，不會被 push 上去。
因此專案 clone 下來之後，必須要重建才能正常執行。

### 開發環境設定

1. 安裝依賴套件

   ```bash
   composer install
   ```

2. 設定.env 檔

   複製.env.example 並更改為.env

   ```bash
   cp .env.example .env
   ```

   修改.env

3. 設定加密的 APP_KEY

   ```bash
   php artisan key:generate
   ```

4. 設定資料庫

   建立 MySQL 所需的資料庫

5. Migration 和 Seeding 建立資料表結構

   ```cmd
   php artisan migrate
   &
   php artisan db:seed
   &
   php artisan migrate --seed
   ```

6. 若有安裝 passport 需運行命令產生 Access Token

   ```bash
   php artisan passport:install
   ```

7. 建立符號連結
   如果有使用到 public storage （如：Storage::disk('public')），
   記得使用以下指令，將 storage 軟連結到 storage/app/public

   ```cmd
   php artisan storage:link
   ```

8. 設定伺服器

   例如到 NGINX 新增、調整 conf 檔

9. 設定任務排程

   如果有在 Laravel 中定義排程的任務，
   記得在 crontab 中增加 Laravel 指令排程器

   ```cmd
   # 在 crontab -e 中
   * * * * * php /path-to-your-project/artisan schedule:run >> /dev/null 2>&1
   ```

### 上線環境設定

1. 安裝 composer 排除 dev 項目

   ```bash
   composer install --optimize-
   loader --no-dev
   ```

2. `.env`設定轉為線上並且關閉錯誤提示

   ```php
   APP_NAME=專案名稱
   APP_ENV=production
   APP_KEY=
   APP_DEBUG=false
   APP_URL=https://正式網址
   ```

3. 設定快取

   ```bash
   php artisan config:cache

   #　下次更新程式記得更新config
   php artisan config:clear
   ```

4. Router 快取  
   error: (Unable to prepare route [api/user] for serialization. Uses Closure. )

   ```bash
   php artisan route:cache

   # 下次更新程式記得更新route
   php artisan route:clear
   php artisan cache:clear
   ```

5. Composer 緩存

   ```bash
   composer dump
   load -o
   # 每次更新composer install 後，都要再執行一次
   ```

6. 類別緩存  
   error: (Unable to prepare route [api/user] for serialization. Uses Closure. )

   ```bash
   php artisan optimize
   ```

7. 清除類別緩存

   ```bash
   php artisan clear-compiled
   ```

8. 建立 keygen

   ```bash
   php artisan key:generate
   ```

9. 若有安裝 passport 需運行命令產生 Access Token

   ```bash
   php artisan passport:keys
   ```

10. 執行

```bash
# 遷移資料表
php artisan migrate
# 填充資料
php artisan db:seed
```

### composer install 失敗

```shell
node: /lib64/libm.so.6: version `GLIBC_2.27` not found (required by node)
node: /lib64/libc.so.6: version `GLIBC_2.28` not found (required by node)
node: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.28` not found (required by node)
```

當出現上面的錯誤訊息，表示 GLIBC 的版本不符合現行系統上的 node 版本。

解決錯誤常見的方法有兩種：

1. 安裝較舊、支援更廣泛的 Node.js (16.x) 版本

   使用 `nvm` 安裝其他版本的 node.js

   ```shell
   nvm install 16
   nvm use 16
   ```

   完成後確認當前版本

   ```shell
   nvm ls
   node --version
   ```

   移除特定版本

   ```shell
   # 👇️ uninstall Node.js version 13.X.X
   nvm uninstall 13
   ```

   若還未安裝 nvm

   ```shell
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
   chmod +x ~/.nvm/nvm.sh
   source ~/.bashrc
   # 驗證 nvm 是否安裝成功
   nvm -v
   ```

2. 將 Linux 操作系統升級到更新版本。

## Laravel ReactJS

> Use laravel/ui Package to install react in laravel with Bootstrap 4.

1. 建立新的專案

   ```cmd
   composer create-project laravel/laravel --prefer-dist running_in_circles

   laravel new running_in_circles
   ```

2. 進入 Laravel 項目

   ```cmd
   cd running_in_circles
   ```

3. 安裝 laravel/ui

   ```cmd
   composer require laravel/ui
   ```

4. 在 Laravel 中安裝 React

   ```cmd
   php artisan ui react
   ```

5. 安裝所需的軟件包

   ```cmd
   <!-- 檢查node和npm是否安裝 -->
       node -v
       npm -v
   <!-- 建立一個node_modules資料夾並自動安裝package.json -->
       npm install
   ```

6. 在 Laravel 中設置 React 組件

   ```javascript
   // 路徑 ==> resource/js/components/User.js
   import React from "react";
   import ReactDOM from "react-dom";

   function User() {
     return (
       <div className="container mt-5">
         <div className="row justify-content-center">
           <div className="col-md-8">
             <div className="card text-center">
               <div className="card-header">
                 <h2>React Component in Laravel</h2>
               </div>
               <div className="card-body">I am tiny React component in Laravel app!</div>
             </div>
           </div>
         </div>
       </div>
     );
   }

   export default User;

   // DOM element
   if (document.getElementById("user")) {
     ReactDOM.render(<User />, document.getElementById("user"));
   }
   ```

7. 修改 resources/js/app.js 註冊 React 文件

   ```javascript
   require("./bootstrap");

   // Register React components
   require("./components/Example");
   require("./components/User");
   ```

8. 修改 views/welcome.blade.php 模板

   ```html
   <!DOCTYPE html>
   <html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
     <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1" />
       <title>Laravel</title>
       <!-- Styles -->
       <link href="{{ asset('css/app.css') }}" rel="stylesheet" />
     </head>

     <body>
       <!-- React root DOM -->
       <div id="user"></div>
       <!-- React JS -->
       <script src="{{ asset('js/app.js') }}" defer></script>
     </body>
   </html>
   ```

9. 執行命令編譯 Laravel 和 React.js

   ```cmd
   npm run watch
   ```

10. 編譯成功，運行 laravel

```cmd
php artisan serve
```

## Laravel 安裝 bootstrap

### Laravel 8

1. 終端機

   ```cmd
   npm install
   ```

2. 建立文件(如果尚未建立) `resources/sass/app.scss` 並引入:
   `@import '~bootstrap';`

3. 在 webpack.mix.js 加入

   ```php
   mix.sass('resources/sass/app.scss', 'public/css')
   ```

4. 終端機

   ```cmd
   npm run dev
   ```

5. 現在可以引用 bootstrap

   ```php
   <link href="{{ asset('css/app.css') }}" rel="stylesheet">
   ```

### Laravel 6

1. 終端機輸入

   ```cmd
   composer require laravel/ui="1.*" --dev
   ```

2. 輸入

   ```cmd
   php artisan ui bootstrap
   ```

3. 如果出現 "Command "ui" is not defined."

   ```cmd
   composer update
   ```

4. 執行

   ```cmd
   npm install
   ```

5. 終端機

   ```cmd
   npm run dev
   ```

6. 現在可以引入

   ```php
   <link rel="stylesheet" href="/css/app.css">
   <script src="/js/app.js"></script>
   ```
