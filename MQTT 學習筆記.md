# MQTT

> 目前最新版本為 v5.0 (但 v3.1 版較為普及)

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

- 開啟 server 防火牆的 port: 1883

mosquitto 的 broker 通訊埠號預設為：1833，因此需要在 server 的防火牆開通道

讓外界可以透過這個通道跟 MQTT Broker 溝通

- 打開資訊欄 => 虛擬私有雲網路 => 防火牆 => CREATE FIREWALL POLICY
- 填入
  - 名稱：自訂名稱
  - 目標：選擇網路中所有執行個體
  - 來源 ip 範圍：0.0.0.0/0
- 通訊協定和通訊埠：
  - 指定的通訊協定和通訊埠：tcp:1883
- 點擊建立

## Mosquitto conf 設定與啟動

移動到軟體檔案安裝的目錄下，用編輯器打開 mosquitto.conf。

### 設定使用者須使用帳號密碼連線

在軟體目錄下，新增使用者帳號清單的文件檔，如：userlist.txt。

內容格式為：`帳號:密碼`

```txt
ivan001:A001
ivan002:A002
ivan003:A003
```

之後運行終端機切換至該目錄並執行 `./mosquitto_passwd.exe -U usrlist.txt`

此時再打開 userlist.txt 會發現建立的密碼欄位都會被加密。

接著修改 Mosquitto.conf ，在文件的末端加入以下敘述：

```txt
allow_anonymous false
password_file C:\你的路徑\mosquitto\usrlist.txt
listener 1883
```

- `allow_anonymous false`: 不允許匿名登入
- `password_file` : 指定帳號清單的目錄
- `listener` : 指定遠端登入時可以使用的 PORT

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

- [參考資料](https://swf.com.tw/?p=1009)

### MQTT Explorer

- [參考資料](https://jimirobot.tw/esp32-mosquitto-conf-mqtt-tutorial/)
