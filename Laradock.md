# Laradock

## 安裝

> 需安裝
> docker
> docker-compose

- 將 laradock 的 repository clone 下來

  ```bash
  git clone https://github.com/Laradock/laradock.git Laradock
  ```

## 環境設定

### laradock/.env

- 在 laradock 的資料夾中複製 .env.example 並改名為 .env

  ```bash
  cp .env.example .env
  ```

- 編輯 .env 中的設定

```vim
### Paths #################################################
# Point to the path of your applications code on your host
# 專案要放在本機的哪個資料夾中
# 這邊放在與 laradock 同層級的 test 資料夾中
APP_CODE_PATH_HOST=../test

# Point to where the `APP_CODE_PATH_HOST` should be in the container
# 設定專案要同步到 container 中的哪一個路徑，預設為 /var/www
APP_CODE_PATH_CONTAINER=/var/www

# You may add flags to the path `:cached`, `:delegated`. When using Docker Sync add `:nocopy`
APP_CODE_CONTAINER_FLAG=:cached

# Choose storage path on your machine. For all storage systems
# 設定你的儲存資料(ex. database, redis 內的數據)要存放在哪。
# 這邊是放在跟 laradock 專案同層級的 data 資料夾中
DATA_PATH_HOST=../data
```

### mysql

若 PhpMyAdmin 登不進去，可能是版本問題

```vim
# 預設值
MYSQL_VERSION=latest

# 改為
MYSQL_VERSION=5.7
```

### PhpMyAdmin

```vim
### PHP MY ADMIN ##########################################

# Accepted values: mariadb - mysql
# 連接的 BD (預設為 mysql)
PMA_DB_ENGINE=mysql

# Credentials/Port:

# 預設的使用者
PMA_USER=default
# 預設密碼
PMA_PASSWORD=secret
# sql root 帳號的密碼
PMA_ROOT_PASSWORD=secret
# phpMyAdmin 執行的 port 號
PMA_PORT=8081
PMA_MAX_EXECUTION_TIME=600
PMA_MEMORY_LIMIT=256M
PMA_UPLOAD_LIMIT=2G
```

### apache2

### nginx

## 啟動

- 啟動 laradock

```bash
docker-compose up -d apache2 phpMyAdmin nginx ...想啟動的服務
```

Laradock 會自動啟動包括 php-fpm 在內的 php-fpm 及 workspace，啟動 phpMyAdmin 時也會連帶啟動 mysql

- 進入 laravel 專案

在啟動 laradock 之後，會建立一個 workspace 的 container，此時進入 workspace 建立 laravel 專案

```bash
docker-compose exec workspace bash
```

進入後，terminal 會顯示自己在 `/var/www` 中，此時 `/var/www` 是連接我們在 .env 中設定的資料夾 `../test`
