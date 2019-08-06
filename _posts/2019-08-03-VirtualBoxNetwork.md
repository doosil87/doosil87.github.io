---
layout: post
title: VirtualBox Network 추가
author: Doosil
date: '2019-08-03 00:00:00 +0530'
category: VirtualBox
summary: VirtualBox
tag_name: VirtualBox

---

<br>

# VirtualBox Network 추가

## 1. Virtual Box 네트워크 환경설정

![](/assets/img/posts/virtualenv1.PNG)

- 파일 - 환경 설정 - 네트워크 - 호스트 전용 네트워크에서 해당 네트워크 편집

<br>

![](/assets/img/posts/virtualenv2.PNG)

![](/assets/img/posts/virtualenv3.PNG)

- DHCP 서버 사용시 선택 및 설정

<br>

## 2. Virtual Box VM 네트워크 설정

![](/assets/img/posts/virtualnet1.PNG)

- 해당 VM 오른쪽 클릭 후 설정 - 네트워크 - 어댑터 2에서 네트워크 어댑터 사용하기 체크 후 호스트 전용 어댑터 선택
- VirtualBox Host-Only Ethernet Adapter #1 ~ 3 중 수정했던 네트워크 선택

<br>

## 3. 설정 후 VM에서 확인

```
ifconfig
```

![](/assets/img/posts/virtualnet2.PNG)



- 해당 네트워크가 잘 붙었는지 확인