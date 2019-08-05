---
layout: post
title: PostgreSQL Recovery
author: Chiha Park
date: '2019-07-30 00:00:00 +0530'
category: PostgreSQL
summary: PostgreSQL
thumbnail: posts/postgresql.jpg
tag_name: PostgreSQL
---

<br>

# PostgreSQL 복구 (Recovery)

<br>

## 1. base.tar 압축 해제

- pg_basebackup을 tar 방식으로 Backup을 하면  base.tar 압축 파일이 생성된다.

```
tar -zxvf base.tar.gz -C /data/PGDATABASE/data/pg
```

<br>

## 2. user tablespace backup 압축 해제 및 Symbolic link 생성

- /data/PGDATABASE/data/pg/tablespace_map 확인
- Tablespace 별로 Path를 확인하여 해당 경로에 압축 해제 및 Tablespace 영역 Symbolic link 생성
- Ex) 16385 /data/PGDATABASE/TS01,   16387 /data/PGDATABASE/TS02

```
tar -zxvf 16385.tar.gz -C /data/PGDATABASE/TS01
ln -s /data/PGDATABASE/TS01 /data/PGDATABASE/data/pg/pg_tblspc/

tar -zxvf 16387.tar.gz -C /data/PGDATABASE/TS02
ln -s /data/PGDATABASE/TS02 /data/PGDATABASE/data/pg/pg_tblspc/
```

<br>

## 3. WAL file 이동(pg_xlog) 및 symbolic link 생성

- pg_xlog 영역 분리
- 혹시 남아 있을지 모를 pg_xlog 파일을 분리 된 pg_xlog 디렉토리 영역으로 복사

```
mv /data/PGDATABASE/data/pg/pg_xlog/* /data/PGDATABASE/pg_xlog
rm -rf /data/PGDATABASE/data/pg/pg_xlog
ln -s /data/PGDATABASE/pg_xlog /data/PGDATABASE/data/pg/pg_xlog
```

<br>

---



## 4. PITR(특정 시간으로 복구)를 원할 경우

- 백업 시점으로 복구를 원할 시에는 이 단계를 SKIP
- 백업 시점 이후부터 돌아가고 싶은 시간까지의 archive 파일이 존재해야한다.
- Ex) 000000010000000000000003 000000010000000000000004는 archive 파일이라 가정

<br>

### 1. arch 파일 이동 

```
mv 000000010000000000000003 000000010000000000000004 /data/PGDATABASE/arch
```

<br>

### 2. recovery.conf 파일 생성

- vi /data/PGDATABASE/data/pg/recovery.conf

```
restore_command = 'cp /data/PGDATABASE/arch/%f %p'
archive_cleanup_command = 'pg_archivecleanup /data/PGDATABASE/arch %r'
recovery_target_time = '2019-07-30 12:30:10 EST'
```

---



<br>



## 5. PostgreSQL Start

```
su - pgadmin -c 'pg_ctl start;'
```

