# ubuntu 學習筆記

<!-- TOC -->

- [ubuntu 學習筆記](#ubuntu-學習筆記)
  - [ubuntu 檔案結構](#ubuntu-檔案結構)
  - [權限](#權限)
    - [概述](#概述)
    - [指令](#指令)
      - [`chown` 更改檔案所有權給其他使用者](#chown-更改檔案所有權給其他使用者)
      - [`chmod` 更改檔案權限](#chmod-更改檔案權限)
      - [`&` 背景執行程式](#-背景執行程式)
      - [`jobs` 檢視任務](#jobs-檢視任務)
      - [`fg` 將任務切換至前景執行，`bg` 將任務切換至背景執行](#fg-將任務切換至前景執行bg-將任務切換至背景執行)
      - [`disown` 卸除目前預設的背景行程](#disown-卸除目前預設的背景行程)
      - [`ps aux | less` 顯示所有正在執行中的進程](#ps-aux--less-顯示所有正在執行中的進程)
      - [`kill 10000` 刪除執行中的進程，`kill` 加上 PID 的數字即可](#kill-10000-刪除執行中的進程kill-加上-pid-的數字即可)
      - [`nohup` 讓程式可以在離線或是登出系統後繼續執行](#nohup-讓程式可以在離線或是登出系統後繼續執行)
  - [安裝 php](#安裝-php)
  - [安裝 MySQL](#安裝-mysql)
    - [更新軟體庫](#更新軟體庫)
    - [升級軟體庫](#升級軟體庫)
    - [安裝指令](#安裝指令)
    - [查看 MySQL 版本](#查看-mysql-版本)
    - [設定 root 密碼](#設定-root-密碼)
    - [移除 MySQL](#移除-mysql)
  - [nginx](#nginx)
    - [service nginx restart 執行出現 fail](#service-nginx-restart-執行出現-fail)
    - [完全移除 nginx](#完全移除-nginx)
  - [找不到 sudo](#找不到-sudo)
  - [安裝 composer](#安裝-composer)
    - [更新系統的套件資訊](#更新系統的套件資訊)
    - [下載 composer 並將其設定為全域可執行的指令](#下載-composer-並將其設定為全域可執行的指令)
  - [安裝 Git](#安裝-git)
  - [安裝 Docker](#安裝-docker)
  - [logrotate 記錄檔管理工具](#logrotate-記錄檔管理工具)
    - [安裝](#安裝)
    - [設定檔](#設定檔)
    - [基本設定](#基本設定)
    - [紀錄檔輪替頻率設定](#紀錄檔輪替頻率設定)
    - [紀錄檔壓縮](#紀錄檔壓縮)
    - [測試 logrotate 設定](#測試-logrotate-設定)

<!-- /TOC -->

## ubuntu 檔案結構

- `/bin` 存放 linux / ubuntu 系統啟動和運行時會使用到的執行檔
- `/boot` linux 核心和 RAM disk Image 存放的地方，同時為啟動選單設定檔存放的地方
- `/dev` 所有 linux 核心有認識的設備和裝置的資訊都存放在此資料夾
- `/etc` 所有影響到系統運作的設定檔
- `/home` 系統上所有使用者的家目錄都會放在此資料夾下的資料夾
- `/lib` 此資料夾存放 linux / ubuntu 系統會用到的程式庫及核心模組
- `/lost+found` 若 ubuntu 的檔案系統掛掉了，系統回復後，會將所有無法正確回復的資料放進此資料夾中
- `/media` 作為隨身碟或 CD 之類的可移除裝置的掛載點
- `/mnt` 早期 linux 版本所使用的可移除裝置的掛載點，在 ubuntu 上用來專門做掛載暫時性的檔案系統用
- `/opt` 無法透過套件安裝的軟體，會將程式安裝在此資料夾
- `/proc` 此為一個虛擬的檔案系統，裡面放的是系統正在運行的程序，linux 核心透過此資料夾內的檔案來傳送訊息給執行中的程序
- `/root` 此為 root 帳號的家目錄
- `/sbin` 此資料夾內的檔案大多是超級使用者或 root 可以使用的管理用指令程式
- `/tmp` 系統、軟體和程式用來存放暫時性資料的地方
- `/usr/bin` 無論是 ubuntu 預載的或是使用者自己安裝的程式或軟體，都會被安裝到此資料夾
- `/usr/lib` 此資料夾文存放 /usr/bin 的程式會用到的程式庫
- `/usr/local` 通常透過自己編譯案安裝的程式會被放到此資料夾之下
- `/usr/share` 此資料夾用來存放 /usr/bin 的程式的共用資料
- `/usr/share/doc` 所有軟體的說明文件會放在這邊
- `/var` 用來存放系統上的動態資料，像是網站、log 和郵件類型的資料
- `/selinux` 此資料夾用來存放 SELinux 套件，預設並沒有安裝，因此為空
- `/srv` 為相容 FHS 標準，因此會需要將架網站或 FTP server 等網路服務改放到此資料夾
- `/sys` 此資料夾和 /proc 一樣為虛擬的檔案系統，用途為提供目前系統的各項資訊

## 權限

> [參考資料](https://shian420.pixnet.net/blog/post/344938711-%5Blinux%5D-chmod-****%E6%AA%94%E6%A1%88%E6%AC%8A%E9%99%90%E5%A4%A7%E7%B5%B1%E6%95%B4!)

### 概述

Linux 為多用戶系統，可同時間讓與多用戶使用。

每個文件和目錄都分配了三種類型的身分

- `owner` 創建檔案的人
- `group` 一個 group 可以有很多 user，如果這個 group 的權限為讀跟寫，那此 group 中的 user 都可以讀跟寫
- `others` 所有人

與三種權限

- `r` 可以開啟和讀取檔案。若為資料夾，則可以查看目錄下的內容，但無法修改(重新命名、移動、剪下、刪除)
- `w` 可以新增、刪除、修改檔案。若檔案有 write 的權限，但資料夾沒有，則只能修改檔案內容，無法更改資料夾結構(修改檔名，移動檔案、刪除檔案)
- `x` 執行程式碼的權限。windows 系統中，只要副檔名為 `.exe` 就可以執行，但在 linux 中需要有 execute permission。read 和 write 權限僅能修改程式碼
- `-` 無權限

```bash
root@rexlitemqtt://home/rexlite_public# ll
total 80
drwxr-xr-x 6 rexlite_public rexlite_public  4096 Aug 30 05:34 ./
drwxr-xr-x 4 root           root            4096 Dec 16  2020 ../
-rw------- 1 rexlite_public rexlite_public 14451 Sep  2 01:38 .bash_history
-rw-r--r-- 1 rexlite_public rexlite_public   220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 rexlite_public rexlite_public  3771 Feb 25  2020 .bashrc
drwx------ 3 rexlite_public rexlite_public  4096 Apr 12  2021 .cache/
drwxrwxr-x 3 rexlite_public rexlite_public  4096 Apr 12  2021 .config/
drwxrwxr-x 3 rexlite_public rexlite_public  4096 Apr 12  2021 .local/
-rw------- 1 rexlite_public rexlite_public   102 Dec 16  2020 .mysql_history
-rw-r--r-- 1 rexlite_public rexlite_public   807 Feb 25  2020 .profile
drwx------ 2 rexlite_public rexlite_public  4096 Sep  2 01:10 .ssh/
-rw------- 1 rexlite_public rexlite_public  3994 Aug 29 06:15 .viminfo
-rw-rw-r-- 1 rexlite_public rexlite_public   584 May 19 10:15 README.md
-rw-rw-r-- 1 rexlite_public rexlite_public  6774 Dec 16  2021 max-system.fishbonetw.com.zip
-rw-rw-r-- 1 rexlite_public rexlite_public  4275 Jul 26  2021 max-system.japhne.com-bluehost.zip
-rw-r--r-- 1 root           root               0 Dec 16  2020 var_log.json
```

第一個字元， `d` 代表 directory，`-` 代表 file

接下來為三個字元一組，分別代表 `user(owner)`、`group`、`other` 及其擁有的權限

- 各權限的分數

  | 字元 | 分數 |
  | :--: | :--: |
  |  r   |  4   |
  |  w   |  2   |
  |  x   |  1   |
  |  -   |  0   |

  分數是累加的，例如 `-rwxrwx---`

  |  字元  | 權限 |  分數   |
  | :----: | :--: | :-----: |
  | owner  | rwx  | 4+2+1=7 |
  | group  | rwx  | 4+2+1=7 |
  | others | ---  | 0+0+0=0 |

  所以該檔的權限數字為 770

第一個帳號為擁有者，第二個群組

### 指令

#### `chown` 更改檔案所有權給其他使用者

- `-R` 針對檔案或是目錄下檔案做遞歸處理(整個目錄下每一個檔案不遺漏處理)

- 將 home 底下 video 目錄所有者，改為 user

  ```bash
  chown user /home/video
  ```

- 將 home 底下 video 目錄的所有者，改成 user，擁有群組改為 video

  ```bash
  chown user:video /home/video
  ```

- 將 home 底下 video 目錄與目錄裡面所有檔案，擁有者改為 user

  ```bash
  chown -R user /home/video
  ```

#### `chmod` 更改檔案權限

- 用數字類型改變檔案權限

  ```bash
  chmod -R xyz < filename | directory >
  ```

  - 若要將.bashrc 這個檔案所有權限都設為啟用

    ```bash
    chmod 777 .bashrc
    ```

  - 若要將檔案權限，改為可執行檔，且不開放修改。  
    則權限為 `-rwxr-xr-x` ，其分數為 755。

  - 若要檔案不希望其他人看到。  
    其權限為 `-rwxr-----`，分數為 740。

- 符號類型改變檔案權限

  - `+` 加入
  - `-` 除去
  - `=` 設定

  ```bash
  chmod [ u | g | o | a ] [ + | - | = ] [ r | w | x ] < filename | directory >
  ```

  - 若要將 `.bashrc` 權限設為 `-rwxr-xr-x`

    - `user` 可讀、可寫、可執行
    - `group` | `others` 可讀、可執行

    ```bash
    chmod u=rwx, go=rx .bashrc
    ```

  - 若權限為 `-rwxr-xr--`

    ```bash
    chmod u=rwx, g=rx, o=r < filename >
    ```

  - 若不知道此檔案的權限，但想要將此檔案設定為全部人都可以寫入  
    `chmod a+w < filename >`
  - 若要將權限去除，而不更動其他已存在的權限  
    `chmod a-x < filename >`

#### `&` 背景執行程式

- 在執行程式後面加上 `&` 使程式可以在背景執行

```bash
php subscribe_with_auth.php &
```

#### `jobs` 檢視任務

```bash
jobs -l
[1]+ 1040421 Running php subscribe_with_auth.php &
```

#### `fg` 將任務切換至前景執行，`bg` 將任務切換至背景執行

```bash
jobs -l
[1]+ 1040421 Running php subscribe_with_auth.php &
```

#### `disown` 卸除目前預設的背景行程

- `disown -a` 卸除所有工作，無論其狀態是否在執行中或是暫停
- `disown -ar` 僅卸除所有執行中的工作
- `disown -h` 不要卸除工作，只是單純讓程式可以在登出後繼續執行。

```bash
jobs -l
[1]+ 1040421 Running php subscribe_with_auth.php &
```

#### `ps aux | less` 顯示所有正在執行中的進程

#### `kill 10000` 刪除執行中的進程，`kill` 加上 PID 的數字即可

```bash
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.3 173272 12812 ?        Ss    2022  85:07 /lib/systemd/systemd --system --deserialize 23
root           2  0.0  0.0      0     0 ?        S     2022   0:06 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<    2022   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<    2022   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<    2022   0:00 [netns]
root           7  0.0  0.0      0     0 ?        I<    2022   0:00 [kworker/0:0H-events_highpri]
root           9  0.0  0.0      0     0 ?        I<    2022   2:45 [kworker/0:1H-events_highpri]
```

#### `nohup` 讓程式可以在離線或是登出系統後繼續執行

當 Linux 使用者登出系統後正在執行的程式會接收到 SIGHUP(hangup) 信號，收到信號的程式會立刻停止執行。

```bash
nohup /path/my_program &
```

## 安裝 php

```bash
apt install -y php7.4 php7.4-cli php7.4-fpm php7.4-mbstring php7.4-xml php7.4-bcmath php7.4-curl php7.4-gd php7.4-mysql php7.4-opcache php7.4-zip php7.4-sqlite3
```

- 查看是否安裝成功

  ```bash
  php -v
  ```

- 安裝最新版 php

由於通常 ubuntu 的套件資訊不會包含最新版本的 php，若需要最新版本，需添加第三方的套件資訊

```bash
apt install -y software-properties-common
```

將第三方套件資訊加入 ubuntu 套件資訊庫

```bash
add-apt-repository -y ppa:ondrej/php
```

更新套件資訊

```bash
apt-get update
```

更新後即可安裝最新的 php

## 安裝 MySQL

### 更新軟體庫

```zsh
apt update
```

### 升級軟體庫

```zsh
apt upgrade
```

### 安裝指令

```zsh
apt install mysql-server -y
```

### 查看 MySQL 版本

```zsh
mysql --version
# output
mysql  Ver 8.0.31-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))
```

### 設定 root 密碼

```zsh
root@DESKTOP-O8SANAT ~ sudo service mysql start

root@DESKTOP-O8SANAT ~ sudo mysql

mysql>  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'SetRootPasswordHere';
Query OK, 0 rows affected (0.01 sec)

mysql> exit
Bye

root@DESKTOP-O8SANAT ~ sudo mysql_secure_installation
Enter password for user root:
# 輸入：SetRootPasswordHere
```

- [設定 root 帳號與初始權限](https://www.albert-yu.com/blog/mysql%E8%A8%AD%E5%AE%9Aroot%E5%B8%B3%E8%99%9F%E5%AF%86%E7%A2%BC%E8%88%87%E5%88%9D%E5%A7%8B%E6%AC%8A%E9%99%90ubuntu-20-04/)

### 移除 MySQL

- 移除 MySQL

```bash
apt-get purge --auto-remove mysql-common mysql-server mariadb-server
apt-get autoremove
apt-get autoclean
```

- 刪除 mysql 使用者

```bash
killall -9 mysql (或 killall -9 mysqld) userdel mysql
```

- 刪除設定檔

```bash
rm -rf /etc/mysql rm -rf /var/lib/mysql
```

## nginx

### service nginx restart 執行出現 fail

[參考資料](https://weijutu.github.io/2019/03/12/web/ubuntu-nginx-restart-fail/)

### 完全移除 nginx

- 停止 nginx 服務

```bash
sudo service nginx stop
```

- 刪除 nginx 及設定文件

```bash
sudo apt-get purge nginx
```

- 自動刪除不使用的軟體包

```bash
sudo apt-get autoremove
```

- 列出與 nginx 相關的軟體，並刪除

```bash
dpkg --get-selections | grep nginx

sudo apt-get purge nginx
sudo apt-get purge nginx-common
sudo apt-get purge nginx-full
```

- 確認 nginx 是否完全刪除

```bash
which nginx
```

## 找不到 sudo

- 先檢查 `/etc/sudoers.d` 檔案是否在，若無則下安裝命令

```bash
apt-get install sudo
```

- 若系統中已經存在 `/etc/sudoers.d` 檔案，表示系統已經安裝 sudo 但尚未設定環境。
  - 用文件編輯器 (vim) 開啟 `/etc/sudoers.d`，找到 `secure_path` 添加路徑

```bash
Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/usr/local/bin
```

## 安裝 composer

> 安裝前須先安裝 PHP Command-Line Interface（PHP-CLI)

### 更新系統的套件資訊

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

### 下載 composer 並將其設定為全域可執行的指令

- 從官網下載 composer 安裝檔至 tmp 資料夾

  ```bash
  php -r "copy('https://getcomposer.org/installer', '/tmp/composer-setup.php')"
  ```

- 驗證下載的安裝檔

  使用 composer 官方提供的 SHA-384 簽章來驗證安裝檔 [Composer Public Keys / Checksums](https://composer.github.io/pubkeys.html)

  輸入驗證的簽章

  ```bash
  php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('/tmp/composer-setup.php'); } echo PHP_EOL;"
  ```

- 安裝 composer
  為了要讓 composer 在全域中使用，所以要將 composer 安裝到 `usr/local/bin` 的資料夾中，以及將 `Composer` 重新命名為 `composer`。

  ```bash
  sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
  ```

- 查看 composer 版本

  ```bash
  composer -V
  ```

- 刪除 composer 的安裝檔

  ```bash
  rm /tmp/composer-setup.php
  ```

## 安裝 Git

```bash
sudo apt-get install git
```

## 安裝 Docker

```bash
sudo apt install docker.io
```

確認安裝

```bash
docker --version
```

啟動 docker

```bash
sudo systemctl start docker

# 啟動時運行
sudo systemctl enable docker
```

## logrotate 記錄檔管理工具

### 安裝

一般而言 linux 發行版都會安裝好 logrotate

```bash
# 安裝 logrotate（Ubuntu/Debian）
sudo apt install logrotate

# 安裝 logrotate（RHEL/CentOS）
sudo yum install logrotate
```

### 設定檔

logrotate 設定檔位於 `/etc/logrotate.conf`，裡面會包含一些預設的設定值，例如紀錄檔的輪替頻率，保留數量等等

```bash
# 每週進行一次記錄檔輪替
weekly

# 記錄檔擁有者與群組為 root 與 syslog
su root syslog

# 保留 4 次輪替的記錄檔
rotate 4

# 輪替之後，自動建立新的記錄檔
create

# 壓縮輪替後的記錄檔
compress

# 套用一般套件的記錄檔設定
include /etc/logrotate.d

# ...
```

個別套件或服務的紀錄檔設定會放在`/etc/logrotate.d` 目錄中，透過這裡的 `include` 來套用個別套件的紀錄檔設定。

```bash
# nginx 記錄檔輪替設定
/var/log/nginx/*.log { # 記錄檔位置
    daily                # 每日輪替一次
    missingok            # 忽略記錄檔不存在問題
    rotate 14            # 保留 14 次輪替的記錄檔
    compress             # 壓縮輪替後的記錄檔
    delaycompress        # 延遲壓縮記錄檔
    notifempty           # 不輪替空的記錄檔
    create 0640 www-data adm # 記錄檔擁有者/群組為 www-data/adm，權限為 0640
    sharedscripts        # 所有記錄檔輪替，只執行一次 prerotate 與 postrotate 指令稿
    prerotate            # 輪替前指令稿
        if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
            run-parts /etc/logrotate.d/httpd-prerotate; \
        fi \
    endscript
    postrotate           # 輪替後指令稿
        invoke-rc.d nginx rotate >/dev/null 2>&1
    endscript
}
```

若在個別服務設定中沒有指定的話，就會套用 `/etc/logrotate.conf` 中預設的設定。

### 基本設定

| 指令           | 說明                                |
| -------------- | ----------------------------------- |
| su root syslog | 紀錄檔擁有者與群組為 root 與 syslog |
| missingok      | 忽略紀錄檔不存在的問題              |
| notifempty     | 不輪替空檔案                        |
| ifempty        | 輪替空檔案                          |
| rotate 7       | 保留七次輪替紀錄                    |

### 紀錄檔輪替頻率設定

| 指令      | 說明            |
| --------- | --------------- |
| daily     | 每日輪替        |
| weekly    | 每週輪替        |
| monthly   | 每月輪替        |
| yearly    | 每年輪替        |
| size 100k | 當檔案超過 100K |
| size 2m   | 當檔案超過 2M   |
| size 1G   | 當檔案超過 1G   |

進階設定

| 指令         | 說明                                                            |
| ------------ | --------------------------------------------------------------- |
| minage 3     | 三天以內建立的檔案不輪替                                        |
| maxage 30    | 不保留三十天以前的紀錄檔                                        |
| maxsize 100k | 搭配 daily 等間隔條件使用，檔案超過 100k 或達到間隔條件時輪替   |
| minsize 100k | 搭配 daily 等間隔條件使用，檔案超過 100k 同時達到間隔條件時輪替 |

### 紀錄檔壓縮

| 指令            | 說明                 |
| --------------- | -------------------- |
| compress        | 壓縮輪替後的舊紀錄檔 |
| nocompress      | 不壓縮輪替後的檔案   |
| delaycompress   | 延遲壓縮紀錄檔       |
| nodelaycompress | 不延遲壓縮紀錄檔     |

### 測試 logrotate 設定

修改完設定後，可以用以下指令測試設定檔是否正確

```bash
# 測試 logrotate 設定
sudo logrotate -d /etc/logrotate.conf
```

如果沒出現錯誤訊息，就完成了

logrotate 是透過 cron 來觸發的，通常是寫在 /etc/cron.daily/logrotate 中，所以更改 logrotate 設定檔之後，只要確認設定無誤，就會自動生效，不需要重新載入設定檔的動作。
