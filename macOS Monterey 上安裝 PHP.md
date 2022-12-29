# macOS Monterey 上安裝 PHP

> ## 動機
>
> ### 發現問題
>
> 安裝完 MAMP 之後，要用終端機安裝 composer，結果出現`zsh: command not found: php`
>
> ### 原因
>
> MacOS Monterey 版本，預設沒有安裝 PHP。

## 安裝 PHP

[Installing PHP on your Mac](https://daily-dev-tips.com/posts/installing-php-on-your-mac/)

### 安裝 Homebrew

在 terminal 輸入

```terminal
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

顯示路徑問題的解決辦法

```terminal
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/changgenglu/.zprofile

eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 使用 Homebrew 安裝 PHP

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

## 使用 Homebrew 切換 PHP

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
