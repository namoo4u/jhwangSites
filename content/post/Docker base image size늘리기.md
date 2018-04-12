+++
title = "Docker base image size늘리기"
tags = [
  "docker",
  "cloud",
]
categories = [
  "docker",
]
date = "2016-11-23T14:29:35+09:00"

+++

### Docker Image 의 Base Sizse 늘리기
```docker
docker -D info
Containers: 1
 Running: 1
 Paused: 0
 Stopped: 0
Images: 1
Server Version: 1.12.3
Storage Driver: devicemapper
 Pool Name: docker-8:17-35389497-pool
 Pool Blocksize: 65.54 kB
 Base Device Size: 53.69 GB
...
```
docker 의 기본 이미지 사이즈는 10GB 이다. 일반적으로 그냥 docker 를 설치하고 이미지를 실행해서 'df -h'를 통해서 사이즈를 확인하면, 9.99GB가 나온다. 하지만 때때로 10GB가 넘는 경우가 발생한다. 외부 Volume을 연결할 수도 있겠지만, 컨테이너 자체에서 스토리지를 관리하고 싶을때도 있다.
이런 경우, systemd docker.service 에서 아래와 같이 추가해 준다.

```
ExecStart=/usr/bin/dockerd --storage-opt dm.basesize=50G --storage-opt dm.loopdatasize=256G -H fd://
```
덤으로 Docker 에서 사용하고 있는데 loop data size 도 변경이 가능하다.

이후에 기존에 있는 이미지를 삭제하고 다시 받아와야 basesize 가 적용이 된다.
