+++
title = "Hugo Blog를 github.io page 로 서비스하기"
date = "2016-11-08T14:17:23+09:00"
categories  = ["Blog"]
tags = ["blog", "hugo", "github.io"]

+++

### Hugo Blog Server

Hugo blog를 github.com 의 page 에서 서비스 하기 위해서는 우선 두개의 Repository 를 생성한다.
하나는 hugo md 파일을 관리하기 위한 repository 이고 하나는 html 로 redner 된 html을 서비스 하기 위한 page repository 이다.

- hugosite.git
- <사용자계정>.github.io.git

#### git repository 설정하기

````bash
cd hugosite

# 초기 설정하기
git init

# hugo
git remote add origin https://github.com/namoo4u/hugosite.git

# 그리고 public 폴더를 hugosite의 subsite 로 등록한다.
git submodule add -b master https://github.com/namoo4u/namoo4u.github.io.git public

````

#### 새로운 hugo content 만들기

````bash
hugo new "post/새로운 post글.md"
````

#### 새로 작성한 글을 github.io 페이지에 올리기

Hugo 사이트에 deploy.sh 이 있는 데 이것을 가져다가 사용할 수도 있고, 그냥 bash 에서 git 명령어를 통해서 deploy 할 수도 있다.

````git
#!/bin/bash

cd hugosite
hugo
cd public
git add -A
msg="rebuild `date`"
git commit -m "$msg"
git push origin master
````
