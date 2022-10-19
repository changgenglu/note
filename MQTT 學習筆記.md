# MQTT

> 目前最新版本為 v5.0 (但 v3.1 版較為普及)

- [MQTT](#mqtt)
  - [概述](#概述)
    - [MQTT 訊息格式](#mqtt-訊息格式)
    - [重要特色](#重要特色)
  - [在 windows 建立 MQTT 測試主機(Eclipse Mosquitto)](#在-windows-建立-mqtt-測試主機eclipse-mosquitto)
  - [在虛擬主機建立 MQTT Broker (Mosquitto)](#在虛擬主機建立-mqtt-broker-mosquitto)
  - [Mosquitto conf 設定與啟動](#mosquitto-conf-設定與啟動)
    - [設定使用者須使用帳號密碼連線](#設定使用者須使用帳號密碼連線)
    - [重新啟動 Mosquitto](#重新啟動-mosquitto)
    - [啟動 MQTT Broker](#啟動-mqtt-broker)
  - [測試 Broker](#測試-broker)
    - [Chrome 瀏覽器擴充程式：MQTTLens](#chrome-瀏覽器擴充程式mqttlens)
    - [MQTT Explorer](#mqtt-explorer)
  - [安裝身分驗證套件(mosquitto-auth-plugin)](#安裝身分驗證套件mosquitto-auth-plugin)
    - [設置 mosquitto](#設置-mosquitto)
    - [安裝 mosquitto-auth-plug 套件](#安裝-mosquitto-auth-plug-套件)
    - [若 mosquitto 無法正常運行](#若-mosquitto-無法正常運行)
  - [安裝身分驗證套件(mosquitto-go-auth)](#安裝身分驗證套件mosquitto-go-auth)
    - [設置 mosquitto (同 mosquitto-auth-plug 套件)](#設置-mosquitto-同-mosquitto-auth-plug-套件)
    - [安裝 mosquitto-go-auth](#安裝-mosquitto-go-auth)
    - [acl 權限設定](#acl-權限設定)
  - [MQTT Client](#mqtt-client)
    - [安裝](#安裝)
    - [設定 mqtt-client 連線](#設定-mqtt-client-連線)

## 概述

適用於 Server 與 Client 訊息傳 遞的通訊協定

利用訂閱(Subscribe)與發佈(Public)的機制來進行訊息傳遞

因其訊息結構簡單且輕量化，因此非常適合用於硬體效能較低的控制器，或作為物聯網的輕量資料收集應用。

在此架構下，會有三種角色：

- `Broker`: 代理人
- `Publisher`: 訊息發佈人
- `Subscriber`: 訊息訂閱者

訊息發佈者(Publisher)多為感測器，發送資料。訊息訂閱者(Subscriber)為使用者的裝置(pc, 手機)，代理人(Broker)接收來自感測器的資料，透過 Topic 來辨別目標的使用者裝置。有訂閱 Topic 的訊息訂閱，會收到相對應的資料。

### MQTT 訊息格式

- `Control Header`(1 byte)
- `Remaining Length`(1 - 4 bytes)
- `Variable Header`
- `Payload`

Control Header 和 Remaining Length 為必須，後面的 Variable Header 和 Payload 則是依需求決定。

在傳遞的過程中，Publisher 不需要知道 Subscriber 的 IP ，只需要知道 Broker 的位址就可以進行訊息傳遞。

Topic 有階層式設計，用`/`分開，並且有大小寫的差異。

### 重要特色

MQTT 可以針對網路品質(QoS)，決定操作等級

- `QoS Level0`: Publisher 丟訊息給 Broker 後不理
- `QoS Level1`: Publisher 丟出訊息後，Broker 必回傳 PUBACK 以確定訊息有收到，倘若沒收到 PUBACK，Publisher 會再重傳一次資料。(缺點為若回傳 PUBACK 時斷線，Publisher 會判斷傳送失敗而再重傳一次資料，將導致 Subscriber 重複收到相同的資料)
- `QoS Level2`: 在 Publisher 確認 Broker 有收到訊息後，Broker 才將資料傳遞給 Subscriber，且 Subscriber 收到訊息後，也須回傳 PUBACK 給 Publisher，可避免收到重複的訊息，但較佔頻寬。

## 在 windows 建立 MQTT 測試主機(Eclipse Mosquitto)

- 在[官網](https://mosquitto.org/download/)下載。
- 在 `Choose Components` 中，如果勾選 `service` 的選項，MQTT Broker 就會變成 windows 的服務，當開機時便會被執行。(若測試環境，建議手動開啟即可)
- 設定 windows 防火牆：在 MQTT 預設的通訊埠號為 1883 在 windows 是關閉的。
  - 開啟 windows Defender 防火牆設定頁面，按下進階設定。
  - 輸入規則，按下新增規則
  - 選擇連接埠
  - 選擇 TPC 通訊協定與特定本機連接埠 1833
  - 選擇允許連線
  - 選擇私人連線
  - 設定名字，即可完成針對 TPC 連線的 1833 port 輸入規則
  - 接著設定輸出規則，步驟相同，一樣指定 TPC 與埠號 1833
  - 最後確認輸入及輸出正常啟用即可

## 在虛擬主機建立 MQTT Broker (Mosquitto)

- 下載 mosquitto 程式庫

  ```bash
  sudo apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
  ```

- 更新程式庫

  ```bash
  sudo apt-get update
  ```

- 安裝 mosquitto

  ```bash
  sudo apt-get install mosquitto
  ```

  - 安裝好後，borker 會自動運行

- 控制指令

  ```bash
  # 查詢 mosquitto 服務狀態
  systemctl status mosquitto

  # 啟動 mosquitto 服務
  sudo systemctl start mosquitto

  # 停止 mosquitto 服務
  sudo systemctl stop mosquitto

  # 重新啟動 mosquitto 服務
  sudo systemctl restart mosquitto
  ```

- 確認運行

  - 查看 server 狀態

  ```bash
  sudo service mosquitto status
  ```

  - 列舉目前作用中的連線：mosquitto server 預設運作於 port: 1833

  ```bash
  rexlite_public@rexlitemqtt:~$ netstat -an
  Active Internet connections (servers and established)
  Proto Recv-Q Send-Q Local Address           Foreign Address         State
  tcp        0      0 0.0.0.0:1833            0.0.0.0:*               LISTEN
  ```

- 本機測試

  - `-d` debug 模式
  - `-t` 訂閱的主題
  - `-h` broker 的 IP
  - `-m` 發送的內容
  - `-v` 顯示主題名稱

  - 訂閱

    ```bash
    mosquitto_sub -v -d -t <topic> -u <user> -P <Possword>
    ```

  - 推送

    ```bash
    mosquitto_pub -d -t <Topic> -m <Message> -u <User> -P <Possword>
    ```

- 開啟 server 防火牆的 port: 1883

mosquitto 的 broker 通訊埠號預設為：1833，因此需要在 server 的防火牆開通道

讓外界可以透過這個通道跟 MQTT Broker 溝通

- 打開資訊欄 => 虛擬私有雲網路 => 防火牆 => CREATE FIREWALL rule
- 填入
  - 名稱：自訂名稱
  - 目標：選擇網路中所有執行個體
  - 來源 ip 範圍：0.0.0.0/0
- 通訊協定和通訊埠：
  - 指定的通訊協定和通訊埠：tcp:1883
- 點擊建立

## Mosquitto conf 設定與啟動

移動到軟體檔案安裝的目錄下(linux: /etc/mosquitto)，用編輯器打開 mosquitto.conf。

### 設定使用者須使用帳號密碼連線

用 `mosquitto_passwd`，來建立密碼

```bash
mosquitto_passwd -c <password file> <username>
```

- 參數 `-c` 為建立密碼文件，若指定的檔案已存在，將會被覆蓋

若要將更多的使用者添加到現有的文件中，則省略 `-c` 參數

```bash
mosquitto_passwd <password file> <username>
```

若要從密碼文件中刪除用戶

```bash
mosquitto_passwd -D <password file> <username>
```

```txt
allow_anonymous false
password_file C:\你的路徑\mosquitto\usrlist.txt
listener 1883
```

- `allow_anonymous false`: 不允許匿名登入
- `password_file` : 指定帳號清單的目錄
- `listener` : 指定遠端登入時可以使用的 PORT

### 重新啟動 Mosquitto

```bash
sudo systemctl restart mosquitto
```

### 啟動 MQTT Broker

在安裝目錄下輸入

```bash
./mosquitto.exe -c mosquitto.conf -v
```

- `-c` 指定 config 檔
- `-v` verbose mode 詳細模式

當啟動成功會顯示所有 borker 的即時資訊

## 測試 Broker

### Chrome 瀏覽器擴充程式：MQTTLens

- [MQTT 教學（四）：使用 MQTTLens 訂閱與發布 MQTT 訊息](https://swf.com.tw/?p=1009)

### MQTT Explorer

- [| ESP32 教學 | Mosquitto conf 設定與 MQTT 測試](https://jimirobot.tw/esp32-mosquitto-conf-mqtt-tutorial/)

## 安裝身分驗證套件(mosquitto-auth-plugin)

> Ubuntu 20
>
> Mosquitto 2.0  
> [mosquitto-auth-plugin](https://github.com/jpmens/mosquitto-auth-plug)  
> MySQL
>
> [Mosquitto 2.0 + mosquitto-auth-plugin + MySQL](https://www.jmeze.net/2021/06/mosquitto-20-mosquitto-auth-plugin-mysql.html)
>
> [Ubuntu 18 使用 apt 安装 mosquitto auth plugin 与 MySQL](https://www.jianshu.com/p/08b42c170a6a)
>
> [mosquitto 权限验证](https://tongxinmao.com/Article/Detail/id/166)

### 設置 mosquitto

- 安裝所需套件

  ```bash
  apt install gcc g++ make xsltproc docbook-xsl libwebsockets-dev libmysqlclient-dev
  ```

- 卸載舊版本的 Mosquitto

  ```bash
  apt purge mosquitto
  ```

- 從官方安裝 mosquitto 源碼

  ```bash
  wget https://mosquitto.org/files/source/mosquitto-2.0.10.tar.gz
  tar xvf mosquitto-2.0.10.tar.gz
  ```

- 更改 config.mk 設定

  ```config
  WITH_WEBSOCKETS:=yes
  WITH_CJSON:=no
  ```

  - `WITH_WEBSOCKETS` 當需要使用 websockets 連線到 mosquitto 時，才將其開啟。
  - `WITH_CJSON` 將此設定開啟會報錯(未知原因)

- 將 mosquitto 編譯並安裝

  ```bash
  make
  make install
  ```

- 建立 mosquitto 使用者並改變目錄權限

  ```bash
  useradd -r mosquitto
  mkdir /var/log/mosquitto
  chown mosquitto:mosquitto /var/log/mosquitto/
  mkdir /var/lib/mosquitto
  chown mosquitto:mosquitto /var/lib/mosquitto/
  ```

- 建立文件 /etc/systemd/system/mosquitto.service

  ```bash
  touch /etc/systemd/system/mosquitto.service
  ```

  ```service
  [Unit]
  Description=Mosquitto MQTT v3.1/v3.1.1 server
  Wants=network.target
  Documentation=http://mosquitto.org/documentation/

  [Service]
  Type=simple
  User=mosquitto
  Group=mosquitto
  ExecStart=/usr/local/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf
  Restart=on-failure
  SyslogIdentifier=Mosquitto

  [Install]
  WantedBy=multi-user.target
  ```

### 安裝 mosquitto-auth-plug 套件

- git clone

```bash
git clone https://github.com/kmihaylov/mosquitto-auth-plug.git
```

- 在 mosquitto-auth-plug 的目錄底下編輯 config.mk 的副本

```bash
cp config.mk.in config.mk
vim config.mk
```

- 根據實際環境，設定 config.mk

```mk
# mosquitto 源碼
# MOSQUITTO_SRC = /etc/mosquitto-2.0.10
MOSQUITTO_SRC = <your path>/mosquitto

# OPENSSLDIR = /usr/lib/ssl
OPENSSLDIR = <your path>
```

- 可以使用 `whereis openssl` 指令來顯示 OpenSSL 的目錄

- 編譯此套件

```bash
make
```

- errors

```bash
/usr/local/include/mosquitto_plugin.h:167:46: error: unknown type name ‘mosquitto_plugin_id_t’; did you mean ‘mosquitto_property’?
```

修改 `auth-plug.c` 與 `log.c` 檔

```c
#include <mosquitto_broker.h>
#include <mosquitto_plugin.h>
#include <mosquitto.h>
```

- 將編譯完成後生成的 `auth-plug.so` 複製至 mosquitto 的目錄下(不是源碼目錄，是安裝後的目錄)

```bash
cp auth-plug.so /var/lib/mosquitto
```

- 進入 mosquitto 安裝後的目錄(預設為 etc/mosquitto)，複製 mosquitto.conf.example 並在文件最後加入設定

```conf
include_dir /etc/mosquitto/conf.d
```

- 在 mosquitto 目錄下建立 conf.d 資料夾，並新增 auth-plug.conf

```bash
mkdir /etc/mosquitto/conf.d
vim auth-plug.conf
```

```conf
# auth_plugin /var/lib/mosquitto/auth-plug.so
auth_plugin /<your path>/auth-plug.so
auth_opt_backends mysql
auth_opt_log_quiet false
# auth_opt_host localhost
auth_opt_host <your mysql host>
# auth_opt_port 3306
auth_opt_port <your mysql port>
auth_opt_dbname <your mysql schema>
auth_opt_user <your mysql user>
auth_opt_pass <your mysql password>

auth_opt_userquery SELECT pw FROM <your_users_table> WHERE username = '%s'
auth_opt_superquery SELECT COUNT(*) FROM <your_users_table> WHERE username = '%s' AND super = 1
auth_opt_aclquery SELECT topic FROM <your_acls_table> WHERE (username = '%s') AND (rw >= %d)
# auth_opt_superusers Sup
auth_opt_superusers S*
auth_opt_ssl_enabled true
```

- 更改檔案權限

  ```bash
  chown mosquitto:mosquitto auth-plug.conf
  chmod go-rwx auth-plug.conf
  ```

### 若 mosquitto 無法正常運行

使用 `sudo systemctl start mosquitto -l` 啟動 Mosquitto

使用 `sudo systemctl status mosquitto -l` 查看運行狀態

若狀態為失敗，運行輸入 `sudo /usr/local/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf` (ExecStart=)，查看詳細的啟動錯誤資訊

- 缺乏權限：將缺乏權限的目錄或是檔案，其權限歸於 mosquitto
- libmosquitto.so.1:cannot open shard object
  - 運行 `sudo /sbin/ldconfig` 更新庫的連接器緩存

## 安裝身分驗證套件(mosquitto-go-auth)

> 僅支援 Linux (Debian, Ubuntu and Mintus) 和 MacOS
>
> [iegomez/mosquitto-go-auth](https://github.com/iegomez/mosquitto-go-auth#build)
>
> [Mosquitto 安装与部署](https://www.cnblogs.com/IC1101/p/14749722.html)
>
> [Uninstalling Go (golang)](https://askubuntu.com/questions/742078/uninstalling-go-golang)

### 設置 mosquitto (同 mosquitto-auth-plug 套件)

### 安裝 mosquitto-go-auth

- 安裝之前，需先在系統上安裝 golang，所需 go 最低版本為 1.13.8。

```bash
go version
```

- 安裝 go (若要更新 golang，需先將舊版本解除安裝 remove golang)

```bash
# Update the following as per your system configuration
export GO_VERSION=1.16.4
export GO_OS=linux
export GO_ARCH=amd64

wget https://dl.google.com/go/go${GO_VERSION}.${GO_OS}-${GO_ARCH}.tar.gz -O golang.tar.gz
sudo tar -C /usr/local -xzf golang.tar.gz
export PATH=$PATH:/usr/local/go/bin
rm golang.tar.gz

# Prints the Go version
go version
```

- 將 mosquitto-go-auth 套件，git clone 下來，並打包

```bash
make
```

- 設定 mosquitto 文件(mosquitto.conf)

```conf
include_dir /etc/mosquitto/conf.d
```

vim /mosquitto/conf.d/go-auth.conf

```conf
# 套件編譯完成後的檔案
auth_plugin /etc/mosquitto/conf.d/go-auth.so

# 後端
auth_opt_backends mysql

# 密碼編碼方式
auth_opt_hasher bcrypt
auth_opt_hasher_cost 10

# 設定  mosquitto log (上線後應將 log_level debug 關閉)
auth_opt_log_level debug
auth_opt_log_dest file
auth_opt_log_file /var/log/mosquitto/mosquitto.log

auth_opt_mysql_protocol tcp

# 允許使本機密碼
auth_opt_mysql_allow_native_passwords true

# 連接資料庫的設定
auth_opt_mysql_host localhost
auth_opt_mysql_port 3306
auth_opt_mysql_dbname max_system
auth_opt_mysql_user max_system
auth_opt_mysql_password maxsystem@2021
auth_opt_mysql_connect_tries 5
auth_opt_mysql_userquery SELECT password_hash FROM test_user WHERE username = ? limit 1
auth_opt_mysql_superquery SELECT COUNT(*) FROM test_user WHERE username = ? AND is_admin = 1
auth_opt_mysql_aclquery SELECT topic FROM test_acl WHERE test_user_id = (SELECT id FROM test_user WHERE username = ?) AND (rw >= ?)
```

### acl 權限設定

其實一般而言只會使用到權限 2、5、7

```log
0: no access (NULL)
1: read access (r)  // 不會動
2: write access (w)
3: read and write access (rw)
4: subscribe access (s)
5: read & subscribe access (rs)
6: write & subscribe access (ws)
7: read, write and subscribe access (rws)
```

## MQTT Client

> [php-mqtt/client](https://github.com/php-mqtt/client)
>
> [php-mqtt/client-examples](https://github.com/php-mqtt/client-examples)

### 安裝

```git
git clone https://github.com/php-mqtt/client-examples.git
```

將專案複製到本機，進入專案資料夾，啟動 composer

```bash
cd client-examples
composer install
```

### 設定 mqtt-client 連線

進入 share 資料夾，編輯 config.php

```php
<?php

declare(strict_types=1);

define('MQTT_BROKER_HOST', '127.0.0.1');  // host
define('MQTT_BROKER_PORT', 1883);         // port
define('MQTT_BROKER_TLS_PORT', 8883);     // tls port

define('TLS_SERVER_CA_FILE', '');
define('TLS_CLIENT_CERTIFICATE_FILE', '');
define('TLS_CLIENT_CERTIFICATE_KEY_FILE', '');
define('TLS_CLIENT_CERTIFICATE_KEY_PASSPHRASE', null);

define('AUTHORIZATION_USERNAME', '');     // mqtt broker username
define('AUTHORIZATION_PASSWORD', '');     // mqtt broker password
```
