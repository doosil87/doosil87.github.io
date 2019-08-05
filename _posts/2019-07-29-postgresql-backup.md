---
layout: post
title: PostgreSQL Backup
author: Chiha Park
date: '2019-07-30 00:00:00 +0530'
category: PostgreSQL 
summary: PostgreSQL
thumbnail: posts/postgresql.jpg
tag_name: PostgreSQL
---

​    

 <br>

# PostgreSQL 백업 (Backup)

<br>

## PG_BASEBACKUP을 이용한 백업

- /data/PGDATABASE/backup 경로에 백업 파일 저장
- pgsys user를 이용한 백업

  <br>

### 1.  pg_hba.conf 에서 pgsys에게 replication 권한 설정

```
pg_hba.conf 파일에 가장 아래 한줄 추가

# TYPE  DATABASE        USER            ADDRESS                 METHOD
...
local   replication     pgsys                                   trust
```

<br>

### 2. postgresql.conf 에서 wal 관련 설정 변경

```
max_wal_senders = 1
wal_level = replica
```

<br>    

### 3. DB 재실행 및 pg_basebackup 실행

```
pg_ctl restart
pg_basebackup --pgdata="/data/PGDATABASE/backup" --format="t" -X fetch --gzip --progress --verbose --username="pgsys" --port="5432"
```

