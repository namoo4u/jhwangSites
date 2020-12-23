---
title: "jumpbox on vSphere for Tanzu"
date: 2020-12-23T00:00:00+00:00
tags: ["vsphere", "kubernetes", "k8s", "tanzu", "cli"]
---

이 문서는 vsphere 환경에서 tanzu를 설치/설정하기 위해 사용하는 jumpbox 를 손쉽게 사용하기 위한 문서이다.

# deploy OVA

대체로 Private Cloud이든 Public Cloud이든 동일한 작업환경을 위해서 Jumpbox를 설치해서작업을 하며 Jumpbox로는 ubuntu server 를 많이 사용한다. 여기서도 ubuntu-server-20.04.1 (LTS) 을 기준으로 설명한다.

![](/img/jumpbox/deploy-ubuntu-cloudimage-ova.png)

iso로 실제 ubuntu-server를 설치해도 되고, 이미 export 해놓은 ova를 업로드하여 사용할 수 있지만, Public internet이 되는 환경이라면 이미 만들어진 ubuntu-cloudimage 를 사용하는 것도 좋다.

ubuntu cloud image는 https://cloud-images.ubuntu.com 에서 받을 수 있다. kaist 나 daum 같은 ftp mirror 를 제공하는 곳에서는 대체로 ubuntu release(cdimages)나 cd/iso 만 mirror 해서 제공하는데 cloud-image 도 제공해 주면 좋겠다.

https://cloud-images.ubuntu.com 사이트에는 ubuntu 의 각 릴리즈별 이미지를 제공한다.
- windows Azure/Hyper-V images
- Vagrant images
- VMware/Virtualbox OVA
- img

![](/img/jumpbox/deploy-ubuntu-cloudimage-ova-02.png)

# user-data 와 meta-data

cloud image 는 cloud-init과 cloud-config을 사용하도록 만들어진 image라고 보면 되는데, cloud-config은 크게 user-data와 meta-data 라는 두가지 데이터를 통해서 초기 이미지 기동 시 인스턴스에 필요한 설정을 한다.
- user-data : 사용자 데이터
- meta-data : cloud-provider들에 대한 설정 데이터

![](/img/jumpbox/deploy-ubuntu-cloudimage-data.png)

- 기본으로 ubuntu 라는 사용자가 생성되어 있으며, IP address 는 DHCP로 할당 받도록 되어 있다

user-data
```
network:
  version: 2
  ethernets:
    ens192:
      dhcp4: no
      addresses: [10.213.89.254/24]
      gateway4: 10.213.89.1
      nameservers:
        addresses: [10.192.2.10,10.192.2.11,8.8.8.8]
users:
  - default
  - name: ubuntu
    password: VMware1!
    lock_password: False
ssh_pwauth: True
chpasswd: { expire: False}
runcmd:
  - curl -sSL https://raw.githubusercontent.com/jupilhwang/cli-install/master/init.sh | bash - 
```

user-data는 base64 encoded data 로 제공이 되어야 하는데, 위의 yaml을 encode 하면 아래와 같다.

bmV0d29yazoKICB2ZXJzaW9uOiAyCiAgZXRoZXJuZXRzOgogICAgZW5zMTkyOgogICAgICBkaGNwNDogbm8KICAgICAgYWRkcmVzc2VzOiBbMTAuMjEzLjg5LjI1NC8yNF0KICAgICAgZ2F0ZXdheTQ6IDEwLjIxMy44OS4xCiAgICAgIG5hbWVzZXJ2ZXJzOgogICAgICAgIGFkZHJlc3NlczogWzEwLjE5Mi4yLjEwLDEwLjE5Mi4yLjExLDguOC44LjhdCnVzZXJzOgogIC0gZGVmYXVsdAogIC0gbmFtZTogdWJ1bnR1CiAgICBwYXNzd29yZDogVk13YXJlMSEKICAgIGxvY2tfcGFzc3dvcmQ6IEZhbHNlCnNzaF9wd2F1dGg6IFRydWUKY2hwYXNzd2Q6IHsgZXhwaXJlOiBGYWxzZX0KcnVuY21kOgogIC0gY3VybCAtc1NMIGh0dHBzOi8vcmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbS9qdXBpbGh3YW5nL2NsaS1pbnN0YWxsL21hc3Rlci9pbml0LnNoIHwgYmFzaCAtCg==
