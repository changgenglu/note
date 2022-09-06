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

- `read`
  可以開啟和讀取檔案。若為資料夾，則可以查看目錄下的內容，但無法修改(重新命名、移動、剪下、刪除)
- `write`
  可以新增、刪除、修改檔案。若檔案有 write 的權限，但資料夾沒有，則只能修改檔案內容，無法更改資料夾結構(修改檔名，移動檔案、刪除檔案)
- `execute`
  執行程式碼的權限。windows 系統中，只要副檔名為 `.exe` 就可以執行，但在 linux 中需要有 execute permission。read 和 write 權限僅能修改程式碼

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

- `r` read permission
- `w` write permission
- `x` execute permission
- `-` no permission

- 各權限的分數

  | 字元 | 權限分數 |
  | :--: | :------: |
  |  r   |    4     |
  |  w   |    2     |
  |  x   |    1     |
  |  -   |    0     |

  分數是累加的，例如 `-rwxrwx---`

  |  字元  | 權限 |  分數   |
  | :----: | :--: | :-----: |
  | owner  | rwx  | 4+2+1=7 |
  | group  | rwx  | 4+2+1=7 |
  | others | ---  | 0+0+0=0 |

  所以該檔的權限數字為 770

### 指令

- `chown` 更改檔案所有權給其他使用者

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

- `chmod` 更改檔案權限

  - 用數字類型改變檔案權限

    ```bash
    chmod -R xyz < filename | directory >
    ```

    - 若要將.bashrc 這個檔案所有全線都設為啟用

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
