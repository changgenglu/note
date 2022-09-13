# 在 GCP 部署 laravel 專案

###### tags: `網管` `GCP` `Laravel`

> [執行環境設定](https://bugswarehouse.blogspot.com/2018/07/gcpgceubuntuapachelaravel56.html)
>
> - Ubuntu 20.04.1 LTS \n \l
> - Apache/2.4.41 (Ubuntu)
> - mysql Ver 8.0.29-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
> - PHP 7.4.3
> - git 2.25.1
> - Composer 2.0.12

## 設置專案

### clone git repo

將目錄切換到`/srv/www`，將託管在 git repo 的專案 clone 下來並依 laravel 上線環境設定流程執行。
一般而言會將文件名稱設為域名。
將目錄切換到 Apache 主機放公開程式的地方`/var/www`，將設定軟連結指向專案位置

```bash
ln -s /srv/www/your_project.com /var/www/your_project.com
```

### 上線環境設定

1. 安裝 compsoer 排除 dev 項目

   ```bash
   composer install --optimize-autoloader --no-dev
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

4. Composer 緩存

   ```bash
   composer dumpautoload -o
   # 每次更新compsoer install 後，都要再執行一次
   ```

5. 建立 keygen

   ```bash
   php artisan key:generate
   ```

6. 執行資料庫 migrate (須注意資料庫狀態)

   ```bash
   # 遷移資料表
   php artisan migrate

   # 填充資料
   php artisan db:seed
   ```

7. 障礙排除

   - 清除快取

   ```bash
   php artisan config:clear
   ```

   - migrate 指令

   ```bash
   # 還原 --steph 此參數為後退多少版本
   php artisan migrate:rollback
   php artisan migrate:rollback --step=5

   # 重置所有migration
   php artisan migrate:refresh

   # 重置所有migration，並填充資料
   php artisan migrate:refresh --seed
   ```

### 設定專案文件夾的權限

```bash
sudo chgrp -R www-data /srv/www/your_project.com
sudo chmod -R 775 /srv/www/your_project.com/storage
```

## 2. 設定 Apache

- 設定 Aapche server

```bash
cd /etc/apache2/sites-available
cp 000-default.conf your_project.com.conf
```

- 編輯 conf 文件
  - ServerName : 設定伺服器 Domain Name ，此名稱必須已經註冊
  - ServerAdmin : 設定虛擬主機的管理者信箱，不一定要和本機的網站管理者相同
  - ServerAlias : 設定伺服器網域別名
  - DocumentRoot : 指定虛擬主機的網站主目錄
  - ErrorLog : 設定 error_log 所存放的路徑
  - CustomLog : 設定 access_log 所存放的路徑

```vim
<VirtualHost *:80>
    ServerName your_project.com

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/your_project.com/public

    <Directory /var/www/your_project.com>
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

- 啟用 Apache 站點

```bash
# 關閉預設站點
sudo a2dissite 000-default.conf

# 啟用新站點
sudo a2ensite your_project.com.conf

# 首次設定需開啟
sudo a2enmod rewrite

# 重啟Apache
sudo service apache2 restart
```
 
## 參考資料

[Run Laravel on Google Compute Engine](https://medium.com/imarishwa-solutions/run-laravel-on-google-compute-engine-b0403a6a9240)

[GCP/GCE/Ubuntu/Apache/Laravel5.6 踩雷筆記](https://bugswarehouse.blogspot.com/2018/07/gcpgceubuntuapachelaravel56.html)

## 延伸閱讀

[詳解 Ubuntu/CentOS 下 Apache 多站點配置](https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/377669/)

[Apache 之——多虛擬主機多站點配置的兩種實現方案](https://www.796t.com/content/1546761795.html)

[ubuntu設定apache部署多個站點](https://www.796t.com/content/1545633208.html)

[SSL憑證設定](https://ithelp.ithome.com.tw/articles/10081759)