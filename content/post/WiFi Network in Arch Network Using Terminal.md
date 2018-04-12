---
title : "WiFi Network in Arch Network Using Terminal"
date : "2016-10-27T11:29:14+09:00"
categories :
  - "Linux"
tags:
  - "linux"
  - "wireless"
image: ""  
---

### Scanning Network

> Arch Linux 에서 iwconfig 이나 iwlist 를 사용할 수 없다면, wireless_tools 가 설치되어 있는지 확인해 보자.
> $ pacman -S wirelesss_tools

```
# 네트워크 인터페이스 이름을 알기 위해서
iwconfig

# 인터페이스 이름을 알았다면
ip link set <interface> up

# 접속 가능한 WiFi network 을 스캔해 보자
iwlist <interface> scan | less

#
ip link set <interface> down
```


### 1번 방법)  Setup A WiFi using "netctl"
```
# network card 가 가능한지 확인해 본다.
lspci -k

# netctl configuration 을 이용해서 설정
netctl

#
```

### 2번 방법) wifi-menu 를 이용한 방법
```
wifi-menu

```
