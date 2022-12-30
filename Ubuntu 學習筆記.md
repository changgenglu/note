# ubuntu 學習筆記

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

```bashq
jobs -l
[1]+ 1040421 Running php subscribe_with_auth.php &
```

#### `ps aux | less` 顯示所有正在執行中的進程

#### `kill 10000` 刪除執行中的進程，`kill` 加上 PID 的數字即可

#### `nohup`

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

## 完全移除 nginx

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
  composer -v
  ```

- 刪除 composer 的安裝檔

  ```bash
  rm /tmp/composer-setup.php
  ```
