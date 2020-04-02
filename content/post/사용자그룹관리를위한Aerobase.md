---
title: "사용자/그룹 관리를 위한 Aerobase"
date: 2020-03-31T00:00:00+00:00
draft: true
---

# Aerobase
모든 시스템 솔루션에는 IAM(Identity & Access Management)이 필요하다. 쉽게는 LDAP을 위한 OpenLDAP이나 OpenDS같은것을 생각할 수 있으나, Keycloak기반의 Aerobase가 최근에 인기가 있다.
단순 IAM 뿐만 아니라 SSO 솔루션(OAuth2, OIDC, SAML)으로도 사용이 가능하다.

https://aerobase.io

- Free: unlimited application and back-end protection without any charges
- Multi-platform support: single UI and API to deliver OAuth2
- Multi-SDK support: provides SDKs

## OpenID Connect
public cloud 인 https://cloud.aerobase.io/portal 에 사용자 계정을 생성하여, 사용할 수도 있다.

## 설치
- Download Aerobase Server
  - aerobase-2.42.[deb|rpm|msi]
  - aerobase-iam-2.4.2.[deb.rpm|msi]

- Install packages
  - yum/apt install openjdk-8-jdk
  - yum/apt install aerobase_-2.4.2*.[rpm/deb] aerobase-iam-2.4.2*.[rpm/deb]

- Install packages with docker
  - docker run -d aerobase/aerobase

- Configure the external URL
  - 외부접속을 위해 FQDN 설정
