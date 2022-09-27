# Git 學習筆記

###### tags: `Git`

- [Git 學習筆記](#git-學習筆記) - [tags: `Git`](#tags-git)
  - [常用指令](#常用指令)
    - [Git 常用指令](#git-常用指令)
    - [Git Bash 常用指令， rm 與 Windows 檔案管理指令對照](#git-bash-常用指令-rm-與-windows-檔案管理指令對照)
  - [Git Flow 開發流程觀念](#git-flow-開發流程觀念)
    - [分支介紹](#分支介紹)
      - [長期分支](#長期分支)
      - [任務分支(Topic)](#任務分支topic)
    - [Git Commit 規範](#git-commit-規範)
      - [Commit Message 格式](#commit-message-格式)
      - [Header Type](#header-type)
      - [Body](#body)
      - [Footer](#footer)
      - [commit 模板](#commit-模板)
  - [Git 操作情境](#git-操作情境)
    - [取消 commit：git reset](#取消-commitgit-reset)
      - [確認 git 紀錄](#確認-git-紀錄)
      - [利用相對位置取消 commit](#利用相對位置取消-commit)
      - [利用絕對位置取消 commit](#利用絕對位置取消-commit)
    - [git commit 打錯字](#git-commit-打錯字)
    - [轉移資料庫：git mirror](#轉移資料庫git-mirror)
    - [將未完成的工作暫存：git stash](#將未完成的工作暫存git-stash)
      - [將現階段工作暫存](#將現階段工作暫存)
      - [取出暫存](#取出暫存)
    - [解決合併衝突](#解決合併衝突)
    - [更改 git remote 位置](#更改-git-remote-位置)
    - [取消 mrege (清除合併紀錄)](#取消-mrege-清除合併紀錄)
    - [新增遠端儲存庫](#新增遠端儲存庫)
  - [Git 管理](#git-管理)
    - [使用 VSCode 管理 Git](#使用-vscode-管理-git)
  - [GitHub 操作](#github-操作)
    - [將本地專案上傳到 github](#將本地專案上傳到-github)
    - [Https 設定 Token](#https-設定-token)
      - [設定 personal access token](#設定-personal-access-token)
    - [設定 SSH](#設定-ssh)
      - [輸入指令產生 SHH](#輸入指令產生-shh)
      - [產生 SSH 連線所需的公鑰內容](#產生-ssh-連線所需的公鑰內容)
      - [上傳公鑰](#上傳公鑰)

## 常用指令

### Git 常用指令

- `git init` 將目前的目錄初始化為 Git 目錄, 建立本地儲存庫
- `git config` 設定或檢視 Git 設定檔資訊
- `git add` 將檔案加入 Git 暫存區
- `git rm` 將檔案移出 Git 暫存區
- `git status` 顯示 Git 狀態
- `git commit` 將暫存區的檔案提交至儲存庫納入版本控制
- `git log` 顯示過去歷次的版本異動
- `git reflog` 顯示完整的版本異動歷史紀錄
- `git show` 顯示指定版本的異動內容
- `git branch` 建立一個新分支 (branch)
- `git checkout` 取出分支內容還原為工作目錄
- `git merge` 合併分支
- `git reset` 重設某一版本
- `git clone` 從遠端儲存庫 (GitHub 或 Bitbucket) 複製副本至本地儲存庫
- `git push` 將本地儲存庫內容推送到遠端儲存庫
- `git pull` 將遠端儲存庫拉回合併更新到本地儲存庫

### Git Bash 常用指令， rm 與 Windows 檔案管理指令對照

- `pwd` | `cd` 顯示幕前目錄
- `ls -al` | `dir` 顯示目前目錄下的檔案與子目錄列表
- `mkdir tmp` | `md tmp` 建立子目錄 tmp
- `rm -r tmp` | `rd tmp` 刪除子目錄 tmp
- `cd tmp` | `cd tmp` 切換至子目錄 tmp
- `cd ..` | `cd ..` 切換至上一層目錄
- `touch test.txt` | `copy nul > test.txt` 建立空白文字檔案
- `cat file/more` | `type file` 顯示檔案內容
- `rm file` | `del file` 刪除檔案 file
- `mv file1 file2` | `ren file1 file2` 將檔案 file1 更名為 file2
- `cp file1 file2` | `copy file1 file2` 複製檔案 file1 為 file2
- `date` | `date` 顯示日期 (Linux 含時間)
- `clear` | `cls` 清除螢幕

## Git Flow 開發流程觀念

> [參考資料：Git Flow 是什麼？為什麼需要這種東西？](https://gitbook.tw/chapters/gitflow/why-need-git-flow)
>
> [參考資料：Git flow 分支策略](https://git-tutorial.readthedocs.io/zh/latest/branchingmodel.html)

### 分支介紹

#### 長期分支

- **main**(原為 master, 於 2020/10 變更)  
   主要為穩定，上線的版本。不該允許開發者直接 commit 到此分支。  
   一般在專案初期，環境建置好就會拉 develop 分支出去，以維持 main 獨立性。
- **develop**  
   所有開發分支的基礎，當新增/修改功能時，會從此分支切出去，完成後再合併回來。

#### 任務分支(Topic)

- **hotfix**  
   上線版本需緊急修復時，由 main 直接切出的 hotfix 分支，修復完成也會合併至 main 分支。  
   由於 develop 在開發中，若從 develop 切 hotfix 分支，再合併至 main 分支時可能會出現更嚴重的問題。
- **feature**  
   開發新功能時，會從 develop 切出 feature 分支，其命名方式採`feature/功能名稱`。
- **release**  
   由 develop 切出來，正式上線前的最終測試分支，通過後會將 release 合併到 main 以及 develop 確保在 release 時修正的一些問題能同步到 main 與 develop。

### Git Commit 規範

> [Git Commit Message 這樣寫會更好，替專案引入規範與範例](https://ithelp.ithome.com.tw/articles/10228738)

#### Commit Message 格式

```bash
# 標題: <type>(<scope>): <subject>
# - type: feat, fix, docs, style, refactor, test, chore
# - scope: 如果修改範圍為全局修改或難以分配給單個組件，可略
# - subject: 以動詞開頭的簡短描述
#
# 正文: 內文需包含:
# * 程式碼更訂的原因(問題、原因、需求)
# * 調整項目
# * 與先前行為的對比
#
# 結尾:
# - 任務編號(如果有)
# - 重大變化(紀錄不兼容的更動)，
#   以 BREAKING CHANGE: 開頭，後面是對變動的描述、以及變動原因和遷移方法。
#
```

#### Header Type

- **feat** - 新增/修改功能 (Feature)
- **fix** - 修正 Bug (bug fix)
- **docs** - 修改/新增文件 (documentation)
- **style** - 修改程式碼格式或風格，不影響原有運作，例如 ESLint (formatting, missing semi colons, …)
- **refactor** - 重構 or 優化，不屬於 bug 也不屬於新增功能等
- **test** - 增加測試功能 (when adding missing tests)
- **chore** - 增加或修改第三方套件(輔助工具)等 (maintain)
- **perf** - 改善效能 (A code change that improves performance)
- **revert** - 撤銷回覆先前的 commit 例如：revert: type(scope): subject (回覆版本：xxxx)。

#### Body

可選的。與 subject 一樣，使用命令式現在時態， 如 change，而不是 changed 或 changes。  
body 應包括改變的動機，並將其與以前的行為進行對比。也就是說，描述為什麼修改，做了什麼樣的修改，以及開發的思路等，是 commit 的詳細描述。

#### Footer

Breaking Changes 應以單詞 BREAKING CHANGE 開頭：用空格或兩個換行符。後面是對變動的描述和變動的理由。

```bash
BREAKING CHANGE: isolate scope bindings definition has changed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }

    "The removed `inject` wasn't generaly useful for directives so there should be no code using it."
```

如果當前 commit 還原了先前的 commit，則應以 revert：開頭，後跟還原的 commit 的 header。在 body 中必須寫成：This reverts commit \<hash>。其中 hash 是要還原的 commit 的 SHA 標識。

```bash
revert: feat(pencil): add 'delete' option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

#### commit 模板

在~/.gitconfig 新增

```
[commit]
template = ~/.gitmessage
```

新建 ~/.gitmessage

```
# 標題: <type>(<scope>): <subject>
# - type: feat, fix, docs, style, refactor, test, chore
# - scope: 如果修改範圍為全局修改或難以分配給單個組件，可略
# - subject: 以動詞開頭的簡短描述
#
# 正文: 內文需包含:
# * 程式碼更訂的原因(問題、原因、需求)
# * 調整項目
# * 與先前行為的對比
#
# 結尾:
# - 任務編號(如果有)
# - 重大變化(紀錄不兼容的更動)，
#   以 BREAKING CHANGE: 開頭，後面是對變動的描述、以及變動原因和遷移方法。
#
```

## Git 操作情境

### 取消 commit：git reset

Git 的 `reset`指令，比較像是「前往」或是「變成」，並不會真的重新設定。

`reset`後的東西都還可以撿的回來。

#### 確認 git 紀錄

```terminal
git log --oneline
af75a42 (HEAD -> develop) 0327
1baa403 (origin/develop) no message
13fd2dc 0223
a640c49 0222新增
e09ecae init commit
```

#### 利用相對位置取消 commit

```terminal
git reset af75a42^
```

`^`符號表示「前一次」的意思，`af75a42^`是指`af75a42`這個 commit 的「前一次」，`af75a42^^`則是往前兩次，以此類推。

如果要倒退五次可以寫成`af75a42~5`。

另外`HEAD`和`develop`也都指向`af75a42`這個 commit，所以也可以寫成

```terminal
git reset develop^
&
git reset HEAD^
```

#### 利用絕對位置取消 commit

```terminal
git reset 1baa403
```

他會切會到`1baa403`這個 commit，剛好是`af75a42`的前一個 commit，和取消最後一次 commit 的效果一樣。

### git commit 打錯字

先把前一次 add 的內容，保留在 changes to be committed 區域

```terminal
git reset --soft HEAD^
```

接著再重新進行一次 git commit 即可

### 轉移資料庫：git mirror

可以轉移整個 repo 的資訊，包括 beanch, tags

進到專案資料夾，設定新的遠端 git repo 位置

```bash
cd Authentication.git/
git remote set-url --push origin https://github.com/your_name/your_project.git
```

local 更新 remote branch ,最後將整包 push 上去

```bash
git push --mirror
```

或者一個指令直接指向遠端 repo

```bash
git push --mirror https://github.com/your_name/your_project.git
```

### 將未完成的工作暫存：git stash

有時候會有工作做到一半，需要切換到別的分支進行其他任務。  
這時有兩種辦法：

1. 先 `commit` 目前進度，之後再 `reset`，將做一半的東西拆回來繼續做。
2. 使用 `stash`。

先看一下目前的狀態：

```bash
git status
On branch feature/admin_controller
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   app/Http/Controllers/RegionController.php
        modified:   app/Models/Room.php
        modified:   app/Models/User.php

no changes added to commit (use "git add" and/or "git commit -a")
```

#### 將現階段工作暫存

目前正在修改 `app/Http/Controllers/RegionController.php` `app/Models/Room.php` `app/Models/User.php`，使用 `git stash` 把他們存起來。

```bash
git stash
Saved working directory and index state WIP on feature/admin_controller: c745ccb style(MemberController): 修改response的資料與取消註解
```

> **注意**
>
> Untracked 狀態的檔案無法被 stash，需要額外使用 `-u` 參數

看一下目前的狀態

```bash
git status
On branch cat
nothing to commit, working tree clean
```

`git stash list` 可以查看暫存檔案

```bash
git stash list
stash@{0}: WIP on cat: b174a5a add cat 2
```

#### 取出暫存

當任務完成，要把剛剛暫存的東西拿回來

```bash
git stash pop stash@{0}
On branch feature/add_new_api_route
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   app/Http/Controllers/RegionController.php
        modified:   app/Models/Room.php
        modified:   app/Models/User.php

no changes added to commit (use "git add" and/or "git commit -a")
Dropped stash@{0} (8810ecbe89e1c1412c0c47d7fb7ded9f3e29aa53)
```

使用 `pop` 指令，可以將某個 `stash` 拿出來並套到目前的分支上。套用成功之後，套用過的 `stash` 就會被刪除。  
如果沒有指定 `pop` 哪一個 `stash`，將會從編號小的也就是 `stash@{0}` 開始使用，也就是最後存進來的。

要刪除 `stash` 可以用 `drop` 指令

```bash
git stash drop stash@{0}
Dropped stash@{0} (87390c02bbfc8cf7a38fb42f6f3a357e51ce6cd1)
```

如果要把 `stash` 撿回來，但不想刪除，可以使用 `apply`

```bash
git stash apply stash@{0}
```

### 解決合併衝突

當在不同分支中，修改同一檔案的不同行，此時合併不會發生問題。  
倘若修改的是同一行，就會發生合併衝突。

```bash
git merge feature/create_device_model
Auto-merging app-src/app/Http/Controllers/UserController.php
CONFLICT (content): Merge conflict in app-src/app/Http/Controllers/UserController.php
Auto-merging app-src/app/Models/Room.php
Auto-merging app-src/app/Models/User.php
CONFLICT (content): Merge conflict in app-src/app/Models/User.php
Automatic merge failed; fix conflicts and then commit the result.
```

有出現 CONFLICT (content)提示的檔案，為發生合併衝突的檔案。  
此時在檔案中，Git 會將衝突位置標示出來。

```php
<<<<<<< HEAD
當前內容。
=======
要合併的目標分支上歧異的內容。
>>>>>>> featuer/i_am_old_branch
```

修正衝突點後，將修改的檔案暫存，最後進行提交。

```bash
git add --all
git commit
```

### 更改 git remote 位置

當修改 git repo 的名稱或是路徑時，若要在本機進行 push 或是 pull 的指令時，會出現：remote: This repository moved. Please use the new location [new location]

- 解決辦法：重新設定 remote url

  ```bash
  git remote set-url origin https://XXX.git
  ```

  檢查 remote url 是否修改成功

  ```bash
  git remote -v
  ```

### 取消 mrege (清除合併紀錄)

> [Git 實戰技巧 - 取消合併](https://blog.darkthread.net/blog/git-undo-merge/)

當 feature 與 develop 分支的合併位置有誤，想要拆掉重做

```bash
db7915e (HEAD -> dev, feature/mqtt_test) feat: 測試mqtt連線
b65d2d2 (tag: release_v2.0.0, origin/dev) no message
0a198be refactor(firmware index page): 優化firmware前端頁面
539942f (origin/master, origin/HEAD, master) Merge branch 'feature/fix_firmware_download' into dev
1a4515a fix(firmwareController): 修復firmware下載問題
d1e204f docs(README): 修改上線環境設定
```

`git rebase -i` ：重整目標 commit 之後的 commit：重整清單中不會有下指令的 commit 而是顯示其後所有的 commit。

```bash
git rebase -i 0a198be
```

輸入指令之後會進入編輯器

```vim
pick db7915e feat: 測試mqtt連線
pick b65d2d2 no message

# Rebase 539942f..879c462 onto 539942f (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
```

並將要取消的 commit 改為 drop

```vim
drip db7915e feat: 測試mqtt連線
pick b65d2d2 no message
```

```bash
b65d2d2 (HEAD -> dev, tag: release_v2.0.0, origin/dev) no message
0a198be refactor(firmware index page): 優化firmware前端頁面
539942f (origin/master, origin/HEAD, master) Merge branch 'feature/fix_firmware_download' into dev
1a4515a fix(firmwareController): 修復firmware下載問題
```

### 新增遠端儲存庫

```bash
git init

git add .

git commit -m "First commit"
```

添加遠端儲存庫的路徑

```bash
## git remote add origin "remote repository URL"
git remote add origin //fishbone/研發部/軟體區/GitServer/V5/*.git
```

將遠端儲存庫初始化

```bash
## git init --bare "remote repository URL"
git init --bare //fishbone/研發部/韌體區/GitServer/V5/*.git
```

將本地儲存庫內容推送到遠端

```bash
git push --set-upstream origin main
```

## Git 管理

### 使用 VSCode 管理 Git

> [Visual Studio Code 無需輸入 Git 指令，透過界面按鈕就可輕鬆管理 Github 中的專案檔案](https://www.minwt.com/webdesign-dev/22926.html)

## GitHub 操作

### 將本地專案上傳到 github

1. git init
2. git add .
3. git commit -m "init commit"
4. git remote add origin `https://github.com/<username>/<repo>.git`
5. git push -u origin master

### Https 設定 Token

當使用推送，輸入 github 密碼會出現錯誤。

```terminal
changgenglu@masenyuandeMacBook-Air ~ % git push -u origin master
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/changgenglu/your_project.git/': The requested URL returned error: 403
```

大致意思是，密碼驗證於 2021 年 8 月 13 日不再支援，也就是今天不能再用密碼方式去提交程式碼。請用使用 **personal access token** 替代。

#### 設定 personal access token

- 開啟 GitHub.com -> Setting -> Developer settings -> Personal access tokens
- 按下`Generate new token`
- Note 欄位填入 token 的備註
- Expiration 設定 token 的時效
- Select scopes 設定權限（基本全部開啟）
- 按下`Generate token`
- 複製 token 代碼

再次使用終端機推送

```terminal
git push -u origin master
```

輸入 github 密碼的地方，貼上 token 代碼

### 設定 SSH

#### 輸入指令產生 SHH

```terminal
ssh-keygen
```

產生

```terminal
$ Enter file in which to save the key (/Users/changgenglu/.ssh/id_rsa):
# 這行只是確定存在哪
$ Overwrite (y/n)?
# 如果原本就有金鑰會跳出此問題，覆蓋嗎？ (是)
$ Enter passphrase (empty for no passphrase):
$ Enter same passphrase again:
# 輸入密碼，再次確認輸入密碼
```

此處的輸入密碼為使用至個金鑰的密碼，可以選擇不輸入。

#### 產生 SSH 連線所需的公鑰內容

```terminal
cat ~/.ssh/id_rsa.pub
```

輸出實例

```terminal
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFp+A3qe4qm1Dkw66LN/vNGlufX5iC9VERfuUiXHNM5L3hQuz6wO8WuzFv+zDIHRPGUl616oLXTHTqommuO0GZavDo+lbUIRkSBM9j/9tr+hlF4LPTT4ggjOgzLCHTrSyzcmcdykgBfnDgX3aYfZbhCEcWdERUxWFNnDf+YYlNd8L6LMKSIce61nhqiSLNbugDCrE0IH+/1hoS3LNoag9V05Qwo5yZ6srLNJT8uISoqvJv5BwSpBL9ImnePx+LzDiVXlJMisKf1GSXdVuWmVWlKrZOsadk4ZkSNH2cL1wgkNvAUbydWKG9Ag4TfI/khKwUXyhT+7V4jWsJusDXZxafylZma4qeOsaLAN4ScSStnOoSm1CxeNqmPsQpAGbtvx49yB2+c4HFsa68VzcwV1oejhh2E67iqqKK53IFN/qQmYYfhUukY6rgLLHlLkmjLqdVpVcULCP0mMzn4xacFWLwDgOtZK1i97vWaLPyG6hYQQ108zK9i/Cg13p0Z+CUTCs= changgenglu@masenyuandeMacBook-Air.local
```

#### 上傳公鑰

到 Github > Settings > SSH and GPG keys 的設定頁面，選擇 New SSH Key。
