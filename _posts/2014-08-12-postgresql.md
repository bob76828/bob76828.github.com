---
layout:     post
title:      "Install PostgreSQL 9.3.x"
subtitle:   "install postgresql 9.3.x on ubuntu 12.04"
date:       2014-08-12 12:00:00
author:     "bob76828"
---

# Install PostgreSQL 9.3.x

## 安裝PostgreSQL lib

`sudo apt-get install libpq-dev`

## 安裝PostgreSQL

**切到root**

```bash
echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

**切回使用者**

```bash
sudo apt-get update
sudo apt-get install postgresql-9.3 postgresql-contrib-9.3
```

## postgres設定

```bash
sudo -i -u postgres
psql
```

**設定postgres密碼**

```bash
alter user postgres with password 'your_password';
```

**建立user**

```bash
CREATE USER user_name WITH PASSWORD 'your_password';
```

**建立db**

```bash
CREATE DATABASE your_db_name;
```

**設定登入權限**

```
alter role user_name LOGIN;
GRANT ALL PRIVILEGES ON DATABASE your_db_name to user_name;
```

## 設定postgres強制密碼登入

```bash
sudo vim /etc/postgresql/9.3/main/pg_hba.conf
```

找到 _# "local" is for Unix domain socket connections only_
並修改成

```
# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
host    all             all             0.0.0.0/0               trust
```

## 設定postgres允許外部登入

```bash
sudo vim /etc/postgresql/9.3/main/postgresql.conf
```

加入  
`listen_addresses = '*'  # what IP address(es) to listen on;`