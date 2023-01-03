# Docker 學習筆記

- [Docker 學習筆記](#docker-學習筆記)
  - [安裝 Docker (Docker for Windows)](#安裝-docker-docker-for-windows)
  - [基本概念](#基本概念)
    - [Image](#image)
    - [Container](#container)
    - [Repository](#repository)
  - [Hello World](#hello-world)
    - [確認 Image 存在於 Repository](#確認-image-存在於-repository)
    - [建立 Container 並執行](#建立-container-並執行)
    - [列出所有已建立的 container](#列出所有已建立的-container)
    - [移除 container](#移除-container)
  - [ubuntu](#ubuntu)
    - [pull](#pull)
    - [啟動](#啟動)
  - [For Laravel: Laradock](#for-laravel-laradock)
    - [環境要求](#環境要求)
    - [資料結構](#資料結構)
      - [laradock/.env](#laradockenv)
      - [mysql](#mysql)
      - [PhpMyAdmin](#phpmyadmin)
      - [apache2](#apache2)
      - [nginx](#nginx)
    - [啟動](#啟動-1)

> 參考資料：
>
> [Docker 基本知識 以及 Docker Compose 實戰經驗](https://hackmd.io/@leonsnoopy/Sya_DevI7#Docker-%E5%9F%BA%E6%9C%AC%E7%9F%A5%E8%AD%98-%E4%BB%A5%E5%8F%8A-Docker-Compose%E5%AF%A6%E6%88%B0%E7%B6%93%E9%A9%97)
>
> [基礎系統及 docker hub 指令](https://joshhu.gitbooks.io/dockercommands/content/Basics/Basics.html)
>
> [How to Deploy Laravel with Docker on Ubuntu 18.04](https://help.clouding.io/hc/en-us/articles/360010679999-How-to-Deploy-Laravel-with-Docker-on-Ubuntu-18-04)

## 安裝 Docker (Docker for Windows)

> Windows 10
> 需啟用 Hyper-v
> 控制台 -> 程式和功能 -> 開啟或關閉 Windows 功能
> 將"Hyper-v"和"容器"設為開啟

- 下載[Docker for Windows](https://docs.docker.com/desktop/get-started/)

## 基本概念

- docker 主要元件：
  - `image` 映像檔
  - `container` 容器
  - `repository` 倉庫

執行 docker 的主機稱為 Host，當 Host 執行 `docker run` 指令時，Docker 會操作這三個元素。

- Image、container、repository 之間的關係就像光碟一樣：早期世紀帝國等光碟遊戲，會需要搭配其他可讀寫空間（如硬碟），才有辦法執行。
  - image 像光碟片，唯獨且無法獨立執行。
  - container 像硬碟，可讀可寫可執行。
  - repository 像光碟盒，儲存 image。
  - registry 則是光碟零售商。

### Image

- image 包裝了一個執行特定環境所需要的資源。
  每個 image 都有獨一無二的 digest，這是從 image 內容作 sha256 產生的。這個是能讓 image 無法隨意更變內容，維持資料的一致性。

雖然 image 裡有必要的資源，但他無法獨立執行，必須靠 container 間接執行。

### Container

- 基於 image 可以建立出 Container。
  他的概念像是建立一個可讀寫內容的外層，架在 image 之上。實際存取 container 會經過可讀寫層與 image，因此看到的內容會是兩者合併後的結果。

Container 特性與 image 不一樣，因為有可讀寫層，所以 container 可以讀寫，也可以拿來執行。

### Repository

- repository 是存放 image 的空間
  docker 的設計類似`分散式版本控制`的方法來存的方法來存放各種 image，而分散式架構就會有類似 git 的 pull/push 行為，實際做的事情也跟 git 類似：為了要跟遠端的 repository 同步。

另一個與 repository 很像，但容易混用的詞為 Registry。Registry 涵蓋範圍更廣，包含了更多 repository 與身分驗證功能等，通常比較常討論的也是 registry。

目前 docker 上面預設的 registry 為 DockerHub，大多數程式或服務的 image 都可以在上面找到。

## Hello World

```bash
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:7f0a9f93b4aa3022c3a4c147a449bf11e0941a1fd0bf4a8e6c9408b2600777c5
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
```

- `docker run <image name>` 會建立一個 container 並執行，而因為 container 需要基於 image 建立，所以 docker run 有一個必要參數為 `image name`。

### 確認 Image 存在於 Repository

首先先確認 `hello-world` 是否存在於本機的 repository，本機找不到的話，就需要從遠端的 repository 下載。

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:7f0a9f93b4aa3022c3a4c147a449bf11e0941a1fd0bf4a8e6c9408b2600777c5
Status: Downloaded newer image for hello-world:latest
```

### 建立 Container 並執行

確認 image 存在後，即可建立 container 並執行。

- `docker run` 預設的行為是：
  1. 前景建立並執行 container
  2. 等待執行程式結束後，會回到前景的命令提示字元
  3. 該 container 會被標記為結束狀態。

### 列出所有已建立的 container

```bash
docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
6d7e00198a56        hello-world         "/hello"            12 seconds ago      Exited (0) 11 seconds ago                       relaxed_bhabha
```

`STATUS` 表示 container 的狀態，`Exited(0)` 後面的數字為程式結束後回傳的狀態碼，通常 0 為正常結束，非 0 則唯有錯誤。

`CONTAINER ID` 和 `NAMES` 為獨一無二，執行多次 `docker run` 後，會生不一樣名字的 container。

### 移除 container

若一直執行 `docker run` 會使 container 數量不斷增加，一般而言沒有再用到的 container 就會將他移除

```bash
# 移除時使用 CONTAINER ID 和 NAMES 都可以。
docker rm 6d7e00198a56

#移除後再檢查一次
docker ps -a
```

## ubuntu

### pull

```bash
# 搜尋 ubuntu
docker search ubuntu
# 將 ubuntu 的 image pull 下來
docker pull ubuntu
```

查看 docker 的 image，應可以看到最新版本的 ubuntu

```bash
docker images
```

| REPOSITORY | TAG    | IMAGE ID     | CREATED     | SIZE   |
| ---------- | ------ | ------------ | ----------- | ------ |
| ubuntu     | latest | 6b7dfa7e8fdb | 3 weeks ago | 77.8MB |

### 啟動

```bash
> docker run -itd --name ubuntu-test ubuntu
6ffebdd2bce193afe7e3fbaa53c6f2e1dffa845e2c8ec55fef899ba191470dbc
```

進入容器

```bash
> docker exec -it ubuntu-test bash
root@6ffebdd2bce1:/#
```

## For Laravel: Laradock

### 環境要求

> Git
> docker
> docker-compose

- 將 laradock 的 repository clone 下來

  ```bash
  git clone https://github.com/Laradock/laradock.git Laradock
  ```

### 資料結構

#### laradock/.env

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

#### mysql

若 PhpMyAdmin 登不進去，可能是版本問題

```vim
# 預設值
MYSQL_VERSION=latest

# 改為
MYSQL_VERSION=5.7
```

#### PhpMyAdmin

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

#### apache2

#### nginx

### 啟動

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
