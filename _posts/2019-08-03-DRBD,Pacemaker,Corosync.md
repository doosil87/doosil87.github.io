---
layout: post
title: DRBD, Pacemaker, Corosync를 이용한 PostgreSQL HA 구성
author: Doosil
date: '2019-08-02 00:00:00 +0530'
category: DB_HA
summary: PostgreSQL HA
thumbnail: /assets/img/posts/drbdlogo.png
tag_name: PostgreSQL HA


---

​    

 <br>

# DRBD, Pacemaker, Corosync를 이용한 PostgreSQL HA 구성

<br>

## 1. 환경 구성

- VM 2개(VirtualBox 사용)

- Cent OS 7.4 버전기준

- NIC 카드 2개 필요
- IP 5개 필요 (Service IP 2개, Heartbeat용 2개, VIP 1개) 

- VM 당 별도 추가 디스크 설정
- 특별한 표시가 없으면 VM 2대 모두에 해당 되는 내용입니다.

<br>

## 2. OS 설정 

<br>

### 1. Directory 생성 (PGDATABASE로 생성)

```
mkdir /PG/PGDATABASE    (Postgresql Engine 디렉토리)
mkdir /data/PGDATABASE  (Postgresql Data 디렉토리)

mkdir /data/PGDATABASE/data  
mkdir /data/PGDATABASE/data/pg  
mkdir /data/PGDATABASE/pg_xlog   
mkdir /data/PGDATABASE/TS01  
mkdir /arch/PGDATABASE/arch 
```

<br>

### 2. USER 생성 및 디렉토리 설정

```
adduser pgadmin

chown -R pgadmin:pgadmin /PG/PGDATABASE
chown -R pgadmin:pgadmin /data/PGDATABASE

chmod -R 0755 /data/PGDATABASE/data
chmod -R 0755 /data/PGDATABASE/pg_xlog
chmod -R 0750 /arch/PGDATABASE/arch
chmod -R 0700 /data/PGDATABASE/TS01
```

<br>

### 3. SELinux 설정 해제 및 NetworkManager 해제

```
setenforce 0
systemctl stop firewalld
systemctl disable firewalld

systemctl stop NetworkManager
systemctl disable NetworkManager
```

<br>

### 4. Hostname 등록 (/etc/hosts)

```
EX)  10.1.1.11     cluster-node01
     10.1.1.12     cluster-node02
     172.1.1.11   cluster-node01-hb 
     172.1.1.12   cluster-node02-hb
```

<br>

### 5. VG, LV 생성

- 추가 디스크가 /dev/sdb에 있다고 가정

```
vgcreate rootvg -s 64 /dev/sdb
lvcreate --extents 100%FREE -n data rootvg
```

<br>

### 6. 계정 환경 설정 (User .bash_profile)

- su - pgadmin
- vi .bash_profile

```
export PGHOME=/PG/PGDATABASE/pgsql 
export PATH=$PGHOME/bin:$PATH 
export PGDATA=/data/PGDATABASE/data/pg
export PGDATABASE=pgdatabase
export PGUSER=pgadmin 
export PGPORT=5448
```

- source ~/.bash_profile (반영)

<br>

## 3. SW 설치

<br>

### 1. 관련 패키지 설치

```
yum install gcc gcc-c++ make autoconf wget readline readline-devel zlib zlib-devel openssl openssl-devel gettext gettext-devel python python-devel
```

<br>

### 2.  pacemaker, corosync 설치

```
yum install pacemaker corosync
```

<br>

### 3. DRBD 설치

```
rpm -Uvh http://mirror.web24.net.au/elrepo/elrepo/el6/x86_64/RPMS/elrepo-release-6-5.el6.elrepo.noarch.rpm
 
yum install drbd84-utils kmod-drbd84 heartbeat
```

<br>



## 4. DRBD Setting

<br>

### 1. DRBD Conf Setting (/etc/drbd.d/drbd_res01.res)

```
resource "drbd_res01"
{
     protocol C;
     disk {on-io-error detach;}
     syncer {}
		
     on cluster-node01 {
             device /dev/drbd0;
             disk /dev/mapper/rootvg-data;
             address 172.1.1.11:7791;
             meta-disk internal;
     }

     on cluster-node02 {
             device /dev/drbd0;
             disk /dev/mapper/rootvg-data;
             address 172.1.1.12:7791;
             meta-disk internal;
     }
}
```

- protocol C 는 동기 방식을 의미
- 아까 만들어 놓은 LV와 논리 디바이스 /dev/drbd0로 매핑하여 사용
- DRBD는 7791번 포트를 통해 통신

<br>



### 2. DRBD Meta Data 생성 및 실행

```
drbdadm create-md drbd_res01 --force
drbdadm up drbd_res01
drbdadm primary drbd_res01 --force             # Primary Node에서 실행
```

- create-md를 통해 메타 데이터를 생성
- drbd를 실행하고 Primary 노드에서 마지막 명령어로 primary롤 지정

<br>

### 3. DRBD Primary 설정 (※ Primary Node 에서만 실행)

```
drbdadm -- --overwrite-data-of-peer primary drbd_res01 
									#primary --> secondary로 동기화가 되게 설정하는 것
drbdadm status drbd_res01
mkfs.xfs /dev/drbd0		# 파일 시스템 생성
mount /dev/drbd0 /data	# Mount
```

- 동기화 하는동안 drbdadm status를 통해 상태확인

<br>

### 4. 실제 동기화가 되는지 확인

- Primary

~~~
touch /data/a
umount /data
drbdsetup /dev/drbd0 secondary
~~~

- Secondary

```
drbdsetup /dev/drbd0 primary
mount /dev/drbd0 /data
```

- Secondary의 /data 밑에 a 파일이 있는 것 확인

<br>

## 5. Pacemaker Cluster 구성

### 1. hacluster 계정 패스워드 설정 

※ hacluster 계정은 pacemaker 설치 시 자동 생성되는 계정

```
echo 'abcd01' | passwd --stdin hacluster	# 비밀번호 abcd01으로 설정
```

<br>

### 2. pcsd 서비스 Start

```
systemctl enable pcsd
systemctl start pcsd
systemctl status pcsd
```

<br>

### 3. pcs cluster 사용자 인증

```
pcs cluster auth cluster-node01 cluster-node02 cluster-node01-hb cluster-node02-hb -u hacluster -p abcd01
```

<br>

### 4. pcs cluster 구성 (※ Primary Node 에서만 실행)

```
pcs cluster setup --name pg_cl cluster-node01,cluster-node01-hb 
cluster-node02,cluster-node02-hb --force
```

- /etc/corosync/corosync.conf 파일 확인

<br>

### 5. pcs cluster start (※ Primary Node 에서만 실행)

```
pcs cluster start --all
```

<br>

### 6. pcs property 설정 (※ Primary Node 에서만 실행)

```
pcs property set default-resource-stickiness=100
pcs property set stonith-enabled=false
```

- resource-stickiness : the measure of how much a resource wants to stay where it is
- stonith-enabled: fencing 기능 사용 여부

<br>



## 6. PostgreSQL 설치

### 1. DB 엔진 설치

- https://www.enterprisedb.com/download-postgresql-binaries 사이트에서 binary 파일 다운로드

```
tar -zxvf postgresql-9.6.14-2-linux-x64-binaries.tar.gz -C /PG/PGDATABASE
```

<br>

### 2. InitDB 실행 (※ Primary Node 에서만 실행)

- DB user로 실행

```
initdb --pgdata=/data/PGDATABASE/data/pg --xlogdir=/data/PGDATABASE/pg_xlog 
--encoding='UTF8' --locale='C' --username='pgsys'
```

<br>

### 3. PostgreSQL START (DB USER로 실행)

```
pg_ctl start
```

​    <br>

### 4. PostgreSQL 기본 Setting  (DB USER로 실행)

```
psql -d postgres

# pgsys 비밀번호 지정
alter user pgsys password 'password001!';

# USER 생성
CREATE USER pgadmin WITH SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN PASSWORD 'password001!';

# Tablespace 생성
create tablespace TBS01 OWNER "pgadmin" location '/data/PGDATABASE/TS01';

exit로 bash로 나간 후
# Database 생성
createdb -p 5432 -D tbs01 PGDATABASE -O pgadmin
```

<br>

## 7. Postgresql Stop 및 DRBD Down

### 1. Postgresql Stop  (※ Primary Node 에서만 실행)

```
su - pgadmin -c 'pg_ctl stop -mf'
```

<br>

### 2. Data영역 Umount  (※ Primary Node 에서만 실행)

```
umount /data
```

<br>

### 3. DRBD secondary  (※ Primary Node 에서만 실행)

```
drbdadm secondary drbd_res01
```

<br>

### 4. DRBD Down

```
drbdadm down drbd_res01
```

<br>



## 8. Pacemaker Resource 구성

### 1. PostgreSQL Resource Agent 수정 (/usr/lib/ocf/resource.d/heartbeat/pgsql)

```
OCF_RESKEY_pgctl_default=/PG/PGDATABASE/pgsql/bin/pg_ctl
OCF_RESKEY_psql_default=/PG/PGDATABASE/pgsql/bin/psql
OCF_RESKEY_pgdata_default=/data/PGDATABASE/data/pg
OCF_RESKEY_pgdba_default=pgadmin
OCF_RESKEY_pghost_default=""
OCF_RESKEY_pgport_default=5432
OCF_RESKEY_start_opt_default=""
OCF_RESKEY_pgdb_default=template1
OCF_RESKEY_logfile_default=/dev/null
OCF_RESKEY_stop_escalate_default=30
OCF_RESKEY_monitor_user_default=pgsys
OCF_RESKEY_monitor_password_default=password001!
OCF_RESKEY_monitor_sql_default="select now();"
OCF_RESKEY_check_wal_receiver_default="false"
```

- PostgreSQL Resource Agent 설정에 자신에게 맞는 정보 설정

<br>

### 2. Pacemaker Resource 등록 (※ Primary Node 에서만 실행)

```
pcs cluster cib pcs_conf
pcs -f pcs_conf resource create VIP ocf:heartbeat:IPaddr2 ip=10.1.1.13 cidr_netmask=32 op monitor interval="5" timeout="10"
pcs -f pcs_conf resource create drbd_res ocf:linbit:drbd drbd_resource=drbd_res01 op monitor timeout="30" interval="5" role="Master" op monitor timeout="30" interval="6" role="Slave"
pcs -f pcs_conf resource master DataSync drbd_res master-max=1 master-node-max=1 clone-max=2 clone-node-max=1 notify=true
pcs -f pcs_conf resource create Filesystem ocf:heartbeat:Filesystem device="/dev/drbd0" directory="/data" fstype="xfs" options="noatime"
pcs -f pcs_conf resource create DB ocf:heartbeat:pgsql op monitor timeout="20" interval="5"
pcs -f pcs_conf resource group add HA-GROUP VIP Filesystem DB
pcs -f pcs_conf constraint colocation add HA-GROUP DataSync INFINITY with-rsc-role=Master
pcs -f pcs_conf constraint order promote DataSync then start Filesystem
pcs cluster cib-push pcs_conf
```

- CIB(Cluster Information Base) 라는 Raw xml에 설정을 저장 후 업데이트하는 방식
- VIP, DRBD, Filesystem,DB 리소스를 추가
- DRBD에 대해선 Datasync라는 이름으로 Master/Slave 구성
- VIP, Filesystem,DB에 대해서는 HA-Group으로 구성
- HA-Group과 Datasync 그룹은 항상 같은 위치에 위치하도록 설정
- DRBD 구성이 완료 후 FileSystem이 구성되도록 설정

<br>

### 3. Pacemaker 실행 (※ Primary Node 에서만 실행)

```
pcs cluster start --all
```

<br>

### 4. Pacemaker 상태 확인 및 로그 확인

```
pcs cluster status
```

- 로그 확인
  - /var/log/cluster/corosync.log
  - var/log/pcsd/pcsd.log
  - /var/log/pacemaker.log



