---
title: 'Postgres install with source 소스 코드로 설치하기'
date: 2024-06-07T15:17:12+09:00
# draft: true
# summary: ""
# hideSummary: false
tags:
    - "postgres"
---

# PostgreSQL 소스코드로 설치하기
## Download
### Github
```sh
git clone https://github.com/postgres/postgres.git
```
```sh
# 설치하고자 하는 버전으로 변경
cd postgres
git checkout REL_16_3
```

### File Browser
> https://www.postgresql.org/ftp/source/ 

여기서 원하는 버전 다운받으면 된다.
```sh
wget https://ftp.postgresql.org/pub/source/v16.3/postgresql-16.3.tar.gz
tar xvfz postgresql-16.3.tar.gz
```

## Compile

```sh
./configure --prefix=/home/postgres/pgsql --enable-cassert --enable-debug CFLAGS="-ggdb -Og -g3 -fno-omit-frame-pointer"
make
sudo make install
```
--prefix 에는 설치할 경로를 지정해주면 된다.

### 기본 DB 설치
```sh
cd /home/postgres/pgsql/bin
./initdb -D ../data
```

## PostgreSQL 가동
-D 는 데이터 폴더, -l은 로그 파일
```sh
cd /home/postgres/pgsql/bin
./pg_ctl start -D ../data -l ../log/test.log
```

## Add user

```
# postgres user 생성
useradd postgres
```

## Create DB

```

$./home/postgres/pgsql/bin/createdb name

```

```sh
# 다른 명령어 확인
./pg_ctl --help
```

### 설정
```sh
vi /home/postgres/pgsql/data/pg_hba.conf
vi /home/postgres/pgsql/data/postgresql.conf
```

### 접속
커맨드를 통해서 접속하고자 하면 아래 처럼 하면 된다.
```sh
cd /home/postgres/pgsql/bin
./psql -U postgres
```

#### psycopg 접속
psycopg로 접속하려고 하면 socket 파일을 찾을 수 없다고 하면서 연결이 안되는 문제가 발생하였다.

찾아보니 postgresql.conf 파일에 `#unix_socket_directories = '/tmp'` 가 있어서

`conn = psycopg2.connect(host='/tmp', user="postgres", password='1234', port=5432)`

host로 해당 폴더를 넣어주면 해결되었다. 

## Extension 설치

### pgvector

```sh
make
export PG_CONFIG=/home/postgres/pgsql/bin/pg_config
sudo --preserve-env=PG_CONFIG make install
```

### pg_hint_plan

```makefile
# PG_CONFIG = pg_config
PG_CONFIG = /home/postgres/pgsql/bin/pg_config
```

### Makefile
```
PG_CONFIG ?= pg_config
PG_CONFIG ?= /home/postgres/pgsql/bin/pg_config
```

소스 코드를 수정하고 build를 여러번 해야한다면 makefile을 위처럼 수정하는게 편하다.