---
layout: post
title: Wireshark 소개 및 설치
author: Doosil
date: '2019-08-10 00:00:00 +0530'
category: Network
summary: Network
tag_name: Network

---

<br>

# Wireshark 소개 및 설치

## 1. Wireshark 

- Wireshark는 네트워크 패킷을 캡쳐하고 분석하는 오픈소스 도구.
- 자체 프로그램으로 네트워크 트래픽을 캡쳐하는 것이 아니고, 운영체제에서 지원하는 캡처 라이브러리를 이용하여 수집한다. (Linux: libpcap, Window: Winpcap)

<br>

## 2. Wireshark 홈페이지에서 다운 및 설치

![](/assets/img/posts/wireshark1.PNG)

- <https://www.wireshark.org/download.html>
- 설치 파일 다운 후 특이 사항 없이 Next를 누르면 설치 가능 (중간에 winpcap 설치 체크)

<br>

### 3. Wireshark 간단 설명서

![](/assets/img/posts/wireshark2.PNG)

- Wireshark 실행 후 분석하고자 하는 NIC 선택
- 윈도우 상단에 패킷 번호, 시간, Source / Destination IP 주소, 프로토콜 종류, 패킷 크기, Info 정보 제공
- 윈도우 중간에 Network layer에 따라 분류 된 패킷의 헤더 정보 제공
- 윈도우 하단에는 실제 주고받은 내용을 16진수로 제공

<br>

### 4. Wireshark 필터 사용법

![](/assets/img/posts/wireshark3.PNG)

### 1. MAC ID로 필터링

- eth.addr == 00:11:22:33:44:55 (source & Destination 모두)
- eth.src == 00:11:22:33:44:55
- eth.dst == 00:11:22:33:44:55

<br>

### 2. IP 필터링

- ip.addr == 1.1.1.1
- ip.src == 1.1.1.1
- ip.dst == 1.1.1.1

<br>

### 3. Port 필터링

- tcp.port == 80
- tcp.src.port == 80
- tcp.dstport == 80

<br>

### 4. TCP Flag 필터링

- tcp.flags.syn==1
- tcp.flags.ack==1
- tcp.flags.reset==1
- tcp.flags.push==1

<br>

### 5. 명령어 혼합 사용

- ip.addr == 1.1.1.1 and tcp.port == 80
- ip.addr == 1.1.1.1 or ip.addr == 2.2.2.2

