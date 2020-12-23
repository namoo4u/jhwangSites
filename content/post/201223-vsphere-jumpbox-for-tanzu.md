---
title: "jumpbox on vSphere for Tanzu"
date: 2020-12-23T00:00:00+00:00
tags: ["vsphere", "kubernetes", "k8s", "tanzu", "cli"]
---

이 문서는 vsphere 환경에서 tanzu를 설치/설정하기 위해 사용하는 jumpbox 를 손쉽게 사용하기 위한 문서이다.

# Deploy OVA

대체로 Jumpbox 로 ubuntu server 를 많이 사용한다. 여기서도 ubuntu-server-20.04.01 (LTS) 을 기준으로 설명한다.

![](../img/jumpbox/deploy-ubuntu-cloudimage-ova.png)
