---
title: "Packer Oci Machine Image"
date: 2018-04-12T10:44:17+09:00
draft: true
categories :
  - "oracle"
  - "cloud"
tags:
  - "oracle"
  - "cloud"
  - "packer"
  - "oci"
  - "machine image"
image: ""
---

### OCI builder for Packer
Packer는 hashcorp에서 만든 오픈소스로 Machine Image생성 툴이다. AMI, Azure, GCP뿐만 아니라, Oracle Cloud Infrastructure또한 지원을 한다.
물론 기본 이미지를 Provisioning하고 Ansible이나 기타 Tool을 통해서 Configuration이 가능하지만, Packer를 사용하면 미리 이미지를 만들어서 배포 후 바로 실행 할 수 있다.

#### 사전에 알아야 할 사항
- packer를 설치한다
  - Linux의 경우 손쉽게 Repository에서 다운로드해서 설치가능하다

  ```
  yaourt -S packer-io
  ```

  or Download from packer site (https://www.packer.io/downloads.html)

- OCI builder for packer
  - https://github.com/oracle/


###### Oracle OCI
- Availabilty_domain
- base_image_ocid
- compartment_ocid
- fingerprint
- shape
- subnet_ocid

##### optional
  - access_cfg_file : Defaults to "$HOME/.oci/config"
  - image_name : Defaults to DEFAULT
  - key_file
  - pass_phrase
  - region
  - tenancy_ocid
  - user_ocid
  - use_private_ip
  - user_data
  - user_data_file : "./boot_config/myscript.sh"


#### 간단 예제 : Redis Server
```json
{
  "builders": [
    {
      "availability_domain": "UpwH:US-ASHBURN-AD-1",
      "region": "us-ashburn-1",
      "base_image_ocid": "ocid1.image.oc1.iad.aaaaaaaatjsckmcbcmuhl3n42iyuugauuupw5jmv3zkxbnwud7mkyhroddoq",
      "compartment_ocid": "ocid1.compartment.oc1..",
      "image_name": "RedisOCI",
      "shape": "VM.Standard1.1",
      "ssh_username": "ubuntu",
      "subnet_ocid": "ocid1.subnet.oc1.iad.",
      "type": "oracle-oci"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "sleep 30",
        "sudo apt-get update",
        "sudo apt-get install -y redis-server"
      ]
    }
  ]
}
```
```sh
packer build redis.json
```
