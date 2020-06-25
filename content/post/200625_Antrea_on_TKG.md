---
title: "TKG에 Antrea CNI 사용하기"
date: 2020-06-25T00:00:00+00:00
tags: ["kubernetes", "cni", "Antrera", "ovs", "octant"]
---

# Antrea
Antrea 는 OVS(Open vSwitch)를 기본으로 하는 Kubernetes용 네트워킹 솔루션 이다. Layer3/4 역할을 담당하며, 보안과 운영상의 도움을 주도록 설계되었다.

https://github.com/vmware-tanzu/antrea/

![](/img/tanzu/antrea/antrea_overview.svg.png)



# Prerequisites
- NodeIPAMController : K8s에서 enabled 되어 있어야 한다. kubeadm 으로 클러스터를 만들 떄 --pod-network-cidr <cidr> 옵션이 있어야 한다
- Open vSwitch kernel module 이 노드에 있어야 한다

Willam Lam 의 Antrea 설치 문서와 내부 자료를 참고했다.
# 설치
tkg의 새로운 Plan을 만든다. tkg는 ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-*.yaml 을 plan으로 사용하기 때문에 새로운 yaml을 만든다. 

아래에서는 dev 파일을 기본으로 작업하는데, dev와 prod를 동시에 작업해 주는게 좋다.

## Step1 : tkg plan
tkg v1.1 까지는 calico 를 기본 CNI로 사용하기 때문에, cluster-template 에서 calico 설정 정보를 제거한다. 

```bash
sed '/calico-config/,$d' ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-dev.yaml > ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-dev-antrea.yaml
sed '/calico-config/,$d' ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-dev.yaml > ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-prod-antrea.yaml
```

## Step2 : antrea.yaml

```bash
wget -q https://raw.githubusercontent.com/vmware-tanzu/antrea/master/build/yamls/antrea.yml
```

## Step3 : antrea plan 에 antrea.yaml 넣기
step1 에서 새로 만든 plan 에 antrea.yaml 을 추가한다.

```bash
awk '{print "    ", $0}' antrea.yml >> ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-dev-antrea.yaml
awk '{print "    ", $0}' antrea.yml >> ~/.tkg/providers/infrastructure-vsphere/v0.6.4/cluster-template-prod-antrea.yaml
rm antrea.yml
```

## Step4 : Antrea TKG Cluster 만들기

```bash
tkg create cluster antrea-cluster --plan dev-antrea -i vsphere -v 6 -w 3 --config ~/.tkg/config-esxi3.yaml
```

![](/img/tanzu/antrea/tkg-create-cluster-with-antrea.png)

# Antrea plugin for Octant
Octant 는 plugin 시스템을 제공한다. Plugin을 통해서 octant의 기능을 확장할 수 있는데, Antrea Plugin을 통해서 네트워크 속성을 볼 수 있다.

