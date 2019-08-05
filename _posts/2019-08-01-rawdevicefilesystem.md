---
layout: post
title: Raw Device와 File System
author: Chiha Park
date: '2019-07-30 00:00:00 +0530'
category: Linux 
summary: Raw Device와 File System
thumbnail: posts/linux.jpg
tag_name: Linux

---

​    

 <br>



# Raw Device와 File System 차이

- Storage Device를 Access 하는 방법에는 Block Device와 RAW Device로 구분
- Block Device의 Block은 파일시스템의 Block을 뜻함.
- Raw Device 위에 파일시스템이 얹어 있다고 보면 된다.
- OS는 어플리케이션의 IO 요구에 따라 파일 시스템에서 읽어 오느냐, RAW 디바이스(파일 시스템 보다는 더 하위레벨)에서 읽어 오느냐가 Acess방법에 의해 차이가 있다.
- Raw Device는 파일시스템이 없기 때문에 파일, 디렉토리, Access 컨트롤 등을 어플리케이션에서 직접 관리 해야 한다.
- Raw Device를 데이터베이스에서 사용할 때 자체적으로 블록과 익스텐트 등의 스토리 관리 개념을 가지고 있기 때문에 OS레벨에서의 물리적인 데이터 파일 관리만 하면 된다.
- 데이터 베이스에서 Raw Device를 사용하더라도 물리적인 Device(DISK)에 데이터 파일형태로 위치해야 하기 때문에 볼륨매니저 같은 가상 스토리지 개념이 필요하다.
- 일반적인 디스크 I/O
  - Application <-> Library Buffer <-> Operation System Cache <-> File System/Volume Manager <-> Device
- Raw Device 
  - Application <-> Device



 <br>

# File System

 <br>

## 파일 시스템이란

- 운영체제가 파티션이나 디스크에 파일들이 연속되기 하기 위해서 사용하는 방법이고 자료구조이다. 파일들이 디스크상에서 구성되는 방식이다. File System은 mount란 단계를 거쳐서 특정 Block Device를 사용할 수 있게 해주는 것이다.

 <br>

## 파일 시스템의 구조

![](/assets/img/posts/Filesystem.PNG)

- Boot Block : File System의 처음에 위치. OS가 초기화되거나 부팅될 때, 필요한 bootstrap 코드를 포함.
- Super Block: File System에 대한 모든 중요한 정보를 저장하는 곳. File System이 얼마나 큰지, 얼마나 많은 파일을 저장할 수 있는지, 자유 저장공간을 어디에서 찾아야 하는지 등의 다양한 정보를 포함하고 있다.
- inode list: 파일 시스템은 index node라는 형태의 정보로 저장된다. inode는 항목(파일,디렉토리, 심볼릭 링크) 자체의 이름을 제외하고는 다른 모든 파일시스템을 저장한다. 파일이름은 디렉토리에 저장되며 이는 inode의 포인터로 이용된다. Inode list는 inode들의 리스트이며, 관리자에 의해 그 크기가 변경될 수 있다. 파일 시스템의 모든 파일이나 디렉토리는 각기 단 하나의 inode에 의해서 표현된다.
- Data Block: 파일 데이터와 관리 데이터를 저장하고 있는 부분이다. 각각의 데이터 블록은 한번에, 단지 하나의 파일에만 할당될 수 있다.

 <br>



# Raw Device

- Raw Device는 파일 시스템이 set up 되지 않은 disk drive이다. 
- 데이터베이스와 같이 주로 자신들의 캐싱 시스템을 갖고 있는 경우에 서비스 프로그램에서 많이 사용한다.
- 운영체제의 캐싱 시스템은 일반적인 상황에서는 성능에 지대한 영향을 미치지만 데이터베이스 등에서는 이미 자신의 방식으로 캐싱을 하기 때문에 두번의 캐싱으로 인해 부하가 생길 수 있다. 
- 운영체제가 지원하는 부분을 사용하지 않게 하기 위해서 raw device를 사용한다.
- 두 대 이상의 machine과 이 machine들이 공유하고 있는 disk로 구성되는 경우, shared disk를 OS file system으로 구성하면 한쪽에서만 이를 볼 수 있고 동시에 access하는 것이 불가능하다. 그러므로 raw device를 사용해야 하며, raw device를 사용하면 OS를 거치지 않으므로 보다 빠른 Access를 기대할 수 있다.

 <br>

- 장점

1. Raw Device는 OS에 Mount 되지 않는 디스크이므로 OS 커널에 의해 Buffering이 되지 않고 User buffer과 device 간에 직접 data가 전송되므로 disk I/O 성능이 향상되고 CPU Overhead가 감소된다.
2. OS File system의 Overhead를 피할 수 있다.
3. OS buffer size를 줄일 수 있다.

 <br>

- 단점

1. Setup하기 어렵고 Backup 절차가 File System 보다 복잡하다.

2. Raw device와 OS file을 혼합하여 사용할 경우 OS File은 ulimit parameter의 size보다 작아야 한다. 따라서 ulimit을 초과하는 table들은 raw device를 사용하여야 한다.

3. OS는 cylinder 0을 보호하지 못하기 때문에 cylinder 0에서 시작하면 안된다.

   