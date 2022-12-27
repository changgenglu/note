# Ubuntu 安裝 MySQL

## 安裝

### 更新軟體庫

```zsh
apt update
```

### 升級軟體庫

```zsh
apt upgrade
```

### 安裝 MySQL

```zsh
apt install mysql-server -y
```

### 查看 MySQL 版本

```zsh
mysql --version
# output
mysql  Ver 8.0.31-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))
```

## 設定 root 密碼

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
