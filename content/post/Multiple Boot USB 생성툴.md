---
title: "Ventoy: Multiple Boot USB 디스크 생성툴"
date: 2020-04-23T10:46:06+09:00
draft: true
tags: ["util", "usb", "iso"]
---

# Multiple Boot USB
## 다른 툴들
OS 설치를 위해서 USB Disk가 필요한 경우가 가끔씩 있는데, 이런 때 마다 Windows, Linux (Archlinux, Ubuntu, CentOS etc..), MacOS (Hackintosh) 등을 위해서 USB Disk 를 만든다. 이러면 여러개의 USB를 가지고 있어야 하고, 불편하기도 하다. 이럴 때, 하나의 USB Disk 에 여러개의 OS 설치 디스크를 넣고 다니면 편하기도 하다.

대표적인 것들이 아래와 같은 툴들이 있다.

- Yumi : yumi
- MultibootUSB : multibootusb.org

## Ventoy
기존 툴과의 가장 큰 차이점은 기존 툴들이 ISO 압축을 해제하고, 압축 해제된 파일을 USB로 복사하는 방식이다. 
![](https://www.linuxbabe.com/wp-content/uploads/2019/01/yumi-multiboot.png)

하지만, Ventoy 는 ISO파일 자체를 그대로 USB 디스크에 복사하면 된다.

https://github.com/ventoy/Ventoy/releases

단점이라고 한다면, Windows 와 Linux 만 지원한다.

![](/img/utils/ventoy.png)

그리고 USB Disk에 iso 파일을 그대로 복사하면 된다.
다음 부팅시 USB를 선택하면 아래와 같이 목록에서 ISO를 선택해서, OS를 설치할 수 있다.

![](/img/utils/ventoy_screen_uefi.png)


별 다섯개 * * * * * 

