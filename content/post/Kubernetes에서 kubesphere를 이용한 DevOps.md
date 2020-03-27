---
title: "Kubernetes 에서 Kubesphere를 이용한 DevOps"
date: 2020-03-25T10:46:06+09:00
draft: true
---

# Developer Portal with Kubesphere

kubesphere (https://github.com/kubesphere/kubesphere) 의 오픈소스 프로젝트로 쿠버네티스 클러스터에서  사용할 수 있는 웹 UI를 제공합니다. 워크스페이스/프로젝트 단위로 워크로드를 관리하고, Jenkins를 통한 파이프라인도 제공한다. 내부적으로 프로메테우스와 ElasticSearch 를 통한 모니터링/로깅을 제공한다.
OpenPitrix 를 사용한 App Store도 제공한다.

공식 사이트:  https://kubesphere.io

## Environments
  - PKS(Pivotal Container Service) 1.6.1
  - NSX-T 2.4.3
  - vSphere 6.7U3

## PKS Installation with EPMC(Enterprise PKS Management Console)
  - Deploye PKS Management Console 
  - Configuration
  - Deploy
  ![](/img/kubesphere/epmc-configuration.png)

#### Create K8s Cluster

- 클러스터 생성
  ```bash
  pks create-cluster k8s --external-hostname demo-cluster.k8s.kdis.local -p medium -n 5
  ```
- 클러스터 생성 확인
  ```bash
  pks cluster k8s
  ```

#### DNS Setting for K8s Master API LB
생성된 클러스터의 Master API Server의 IP 주소를 DNS에 클러스터 생성 시 입력한 external-hostname으로 등록한다.

# Software
- Helm
- Kubesphere

## Default Storage Class 생성
- Storage Class 생성
```bash
kubectl create -f -<<-EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name:  thick
provisioner: kubernetes.io/vsphere-volume
parameters:
  diskformat: zeroedthick
EOF

kubectl patch storageclass thick -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## Helm
- Download CLI
```bash
wget https://get.helm.sh/helm-v2.16.3-linux-amd64.tar.gz
```

- tiller 서비스 어카운트/클러스터롤 생성
```bash
kubectl apply -f -<<-EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
 - kind: ServiceAccount
   name: tiller
   namespace: kube-system
EOF
```

- helm init
```bash
helm init --service-account=tiller
```
- helm repo update
helm 에서 사용할 repo 추가 및 업데이트하기
```bash
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo update
```

## kubesphere

- Installing
```bash
kubectl apply -f https://raw.githubusercontent.com/kubesphere/ks-installer/master/kubesphere-complete-setup.yaml
```

- Verify 
```bash
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l app=ks-install -o jsonpath='{.items[0].metadata.name}') -f
```

