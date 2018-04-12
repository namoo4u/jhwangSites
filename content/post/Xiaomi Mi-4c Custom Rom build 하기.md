+++
title = "Xiaomi Mi 4c Custom Rom build 하기"
tags = [
  "Android",
  "Xiaomi",
]
categories = [
  "android",
]
date = "2016-11-22T17:24:30+09:00"

+++

## Xiaomi Custom Rom Build

#### Docker Image 는 ubuntu:15.10 를 기준으로 한다.
Docker 실행
```bash
docker run -it --name android ubuntu:15.10
```
#### 필요한 Packages 설치
```bash
apt update -y
apt install -y bison build-essential curl flex git gnupg gperf libesd0-dev liblz4-tool libncurses5-dev libsdl1.2-dev libwxgtk2.8-dev libxml2 libxml2-utils lzop maven openjdk-7-jdk openjdk-7-jre pngcrush schedtool squashfs-tools xsltproc zip zlib1g-dev g++-multilib gcc-multilib lib32ncurses5-dev lib32z1-dev realpath bsdmainutils curl file screen android-tools-adb android-tools-fastboot
```

lib32readline-gplv2-dev 는 찾을 수 없는 package 라고 에러가 발생하여, 이를 빼고 설치를 진행한다.

#### Init repo

```bash
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod +x ~/bin/repo
repo init -u https://github.com/CyanogenMod/android.git -b stable/cm-13.0-ZNH5Y
```

#### Sync
```
repo sync -j4
```
Copy device/xiaomi/libra/local_manifests/libra.xml to $ANDROID_BUILD_TOP/.repo/local_manifests/
```
cp ./device/xiaomi/libra/local_manifests/libra.xml $ANDROID_BUILD_TOP/.repo/local_manifests/
```
#### Sync

repo sync -j4
#### Force sync's

repo sync --force-sync bootable/recovery repo sync --force-sync external/jpeg repo sync --force-sync frameworks/base repo sync --force-sync frameworks/native
#### Sync repo

repo sync -j4
#### Build

lunch cm_libra-userdebug brunch cm_libra-userdebug
