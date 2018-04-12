+++
title = "Nautilus에서 문서 선택해서 Edit시 한글입력문제"
tags = [
  "uim",
  "linux",
]
categories = [
  "linux",
]
date = "2016-11-22T14:48:38+09:00"

+++

#### Linux 에서 한글입력 시 문제

~/.xprofile 에 아래와 같이 추가한다.
```bash
export XIM="uim"
export GTK_IM_MODULE='uim'
export QT_IM_MODULE='uim'
exec uim-xim &
export XMODIFIERS='@im=uim'
exec uim-toolbar-gtk3-systray &
```

1. WPS에서 한글입력 시 한글입력이 되지 않는 문제가 있는데, 이는 uim-xim 을 띄우고, qtconfig-qt4의 interface 에서 ime를 xim 으로 해야 wps에서 한글입력이 제대로 됨


2. Natuilus 에서 문서 선택하고, 마우스 오른쪽 클릭 후 Edit를 선택하면 WPS또는 MS Office에서 한글 입력이 되지 않는 문제가 있음
  하지만, Thunar 나 Command line에서 WPS나 MS Office 를 띄우면 한글입력에 문제가 되지 않음.

- [X] xfce4-terminal
- [X] wps
- [X] MS Office with wine
- [X] firefox / thunderbird
- [X] chrome browser
- [ ] WPS or MS Office through nautilus

##### ???????????
***--> nautlis 를 삭제하고 재 설치 이후에는 잘 됨..***
