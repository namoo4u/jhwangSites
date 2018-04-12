+++
date = "2016-11-17T15:22:30+09:00"
title = "SQL Server Docker image on Linux"
categories = ["linux", "docker"]
tags = ["SQL Server", "docker", "linux", "ms"]

+++
## SQL Server on Linux
정말로 놀라운 소식이 아닐수 없다. **"Microsoft is back"** 이란 말이 헛말이 아니라는 것이 요즘 MS에서 나오는 새로운 소식드에서 느낄 수 있다. Visual Studio on mac 에 이어서 몇달전에 발표한 SQL Server on Linux 가 실체를 드러냈다. 리눅스 서버에 새롭게 설치를 할 수도 있겠지만 놀랍게도 Docker image를 통해서 손쉽게 배포를 할 수 있도록 한 것이 또 다른 점이라 할 수 있다.. 이제는 거의 모든 회사에서 새로운 제품을 출시하고 나서 Docker image를 통해서 테스트/운영을 할 수 있도록 제공하는 것이 기본이 되어 버렸다. 이미 Microsoft에서는 수 많은 docker image 를 docker hub에 올려놨다.

```docker
docker search Microsoft
```
<img src="/images/docker_search_microsoft.png" width="950px">

#### Requirements for Docker
- Docker Engine 1.8+
- Minimum of 4 GB of disk storage
- Minimum of 4 GB of RAM

#### Pull and run the Docker image
```docker
docker run -d -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=P@55w0rd' -p 1433:1433 -v /home/jhwang/.mssql:/var/opt/mssql --name mssql microsoft/mssql-server-linux
```
- -e SA_PASSWORD : SA 패스워드 지정
- -p 1433:1433 : MS SQL Server 포트 지정
- -v <host directory>:/var/opt/mssql : host machine의 디렉토리 볼륨을 마운팅한다.

docker image download

<img src="/images/docker_pull_run_mssql_on_linux_01.png" width="950px">

docker 실행 이후 프로세스

<img src='/images/docker_pull_run_mssql_on_linux_02.png' width=950px>

docker logs mssql

<img src='/images/docker_pull_run_mssql_on_linux_04.png' width=950px>

docker container 안에 설치된 mssql

<img src='/images/docker_pull_run_mssql_on_linux_03.png' width=950px>

#### Connect MS SQL Server with IDE
IDE에서 JDBC Driver를 추가하고 MS SQL Server에 연결하기 위해서, 새로운 연결을 생성하고, 테스트를 하는데 제대로 연결이 되지 않는 문제가 있다.
<img src='/images/docker_pull_run_mssql_on_linux_05.png' width=640px>

**docker run 시 SA_PASSWORD는 ms sql server의 Password 정책에 맞아야 한다, 초기에 welcome1 으로 했다가 연결이 되지 않는 문제가 있었는데, Password를 P@55w0rd 로 변경후 제대로 연결이 됨**
<img src='/images/docker_pull_run_mssql_on_linux_06.png' width=640px>
