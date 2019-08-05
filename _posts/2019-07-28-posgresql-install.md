---
layout: post
title: PostgreSQL Installation
author: Chiha Park
date: '2019-07-29 00:00:00 +0530'
category: PostgreSQL
summary: PostgreSQL
thumbnail: posts/postgresql.jpg
tag_name: PostgreSQL
---

<br>

# PostgreSQL 설치 (install)

- PostgreSQL 9.6 버전을 기준으로 Centos 7.4에 설치합니다.
- DB Engine 디렉토리 : /PG/PGDATABASE
- DB DATA 디렉토리: /data

  <br>

## 1. PostgreSQL 파일 다운로드

- https://www.enterprisedb.com/download-postgresql-binaries 사이트에서 binary 파일 다운로드

​    <br>

## 2. 디렉토리 생성

```
mkdir -p /PG/PGDATABASE				# PostgreSQL Engine 영역
mkdir -p /data/PGDATABASE/data/pg  	# PostgreSQL Data 영역
mkdir -p /data/PGDATABASE/pg_xlog   # PostgreSQL xlog 영역 (10버전 이후는 wal 영역)
mkdir -p /data/PGDATABASE/arch      # PostgesSQL Archiving 영역
mkdir -p /data/PGDATABASE/TS01      # PostgreSQL Table Space 영역
```

  <br>

## 3. Kernel Parameter 설정

```
RAM 16GB 기준
sysctl -w kernel.shmmax=17179869184   # 공유 메모리 세그먼트의 최대 크기(바이트 단위)를 정의
sysctl -w kernel.shmall=4194304    # 특정 시점에 시스템에서 사용 가능한 공유 메모리의 최대 크기 (페이지 단위)를 설정하는데 사용
sysctl -w vm.overcommit_memory=2   # Memory Overcommit 을 방지
```

​    <br>

### 4. OS User 생성 및 디렉토리 권한 설정

```
useradd pgadmin						# USER 추가
chown -R pgadmin /PG/PGDATABASE /data/PGDATABASE	
usermod -d /PG/PGDATABASE pgadmin

su - pgadmin
vi ~/.bash_profile

export PGHOME=/PG/PGDATABASE/pgsql
export PATH=$PGHOME/bin:$PATH
export PGDATA=/data/PGDATABASE/data/pg
export PGDATABASE=PGDATABASE
export PGUSER=pgsys
export PGPORT=5432
export PGLOCALEDIR=$PGHOME/share/locale

저장 후 source ~/.bash_profile
```

​    <br>

### 5. DATA 영역 DISK 할당

- DATA 영역에 추가 디스크 할당을 하지 않을 시에는 SKIP

```
exit						# root 권한으로 돌아와서
vgcreate datavg /dev/sdb	# Volume Group 생성 (DATA 영역 디스크가 sdb로 붙어 있을 경우)
lvcreate -l 100%FREE -n data datavg	# Volume Group에 남은 영역 LV에 지정
mkfs.xfs /dev/datavg/data	# CentOS 7버전이므로 xfs로 FileSystem 설치
mount -t xfs /dev/datavg/data /data   # /data Path에 Mount
df -h						# data PATH에 MOUNT 되었는지 확인
```

​    <br>

### 6. PostgreSQL 설치

```
tar -zxvf postgresql-9.6.14-2-linux-x64-binaries.tar.gz -C /PG/PGDATABASE
```

​    <br>

### 7. Initdb 실행 (DB USER로 실행)

```
initdb --pgdata=/data/PGDATABASE/data/pg --xlogdir=/data/PGDATABASE/pg_xlog --encoding='UTF8' --locale='C' --username='pgsys'
```

​    <br>

### 8. PostgreSQL START (DB USER로 실행)

```
pg_ctl start
```

​    <br>

### 9. PostgreSQL 기본 Setting  (DB USER로 실행)

```
psql -d postgres

# USER 생성
CREATE USER pgadmin WITH SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN PASSWORD 'password001!';

# Tablespace 생성
create tablespace TBS01 OWNER "pgadmin" location '/data/PGDATABASE/TS01';

exit로 bash로 나간 후
# Database 생성
createdb -p 5432 -D tbs01 PGDATABASE -O pgadmin
```

