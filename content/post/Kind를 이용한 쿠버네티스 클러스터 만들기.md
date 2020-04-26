---
title: "Kind를 이용한 쿠버네티스 클러스터 만들기"
date: 2020-04-26T00:00:00+00:00
draft: true
tags: ["Kubernetes", "Kind", "K8s", "쿠버네티스"]
---

# Kubernetes 개발환경
## Vagrant vs Minikube vs Kind
Local 환경에서 K8s를 사용하기 위해서 다양한 방법을 사용 할 수 있다.

### Vagrant
Vagrant는 HashCorp에서 만든 프로비저닝 툴로, VagrantFile에 기본 이미지와 생성된 VM에 필요한 설정을 미리 Code화 해서 제공하는 툴이다.
```bash
# vagrant cli 가 설치 되어야 한다.
vagrant version

# git cli 가 설치 되면 좋다.
git version

# 기본 가상화 솔루션으로 VirtualBox를 사용하기 때문에, VirtualBox가 미리 설치가 되어 있어야 한다. Hyper-V나 다른 가상화 툴을 사용할 수 있다.
vagrant up
```

### Minikube
Local 환경에 쿠버네티스를 사용하고 테스트하는데 가장 많이 사용하는 툴이지 않을까 한다.

```bash
# CLI를 다운로드 받아 설치하고, 간단하게 minikube start를 하면 기본 가상화 툴인 virutlabox 에 Single Node Kubernetes Cluster가 기동된다.
minikube start
```

### Kind
https://kind.sigs.k8s.io/

Local 쿠버네티스 클러스터를 기동하기 위해 Docker를 사용한다. 다른 가상화 솔루션 없이, Docker Daemon만 있다면 쿠버네티스 클러스터를 생성할 수 있다.

#### Single Node Cluster
```bash
$ time kind create cluster --name dev --image kindest/node:v1.18.0
Creating cluster "dev" ...
 ✓ Ensuring node image (kindest/node:v1.18.0) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-dev"
You can now use your cluster with:

kubectl cluster-info --context kind-dev

Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
kind create cluster --name dev --image kindest/node:v1.18.0  1.49s user 0.97s system 3% cpu 1:15.15 total
```

클러스터가 제대로 만들어 졌는지 확인 할 수 있다.
```bash
$ kind get clusters
dev
```

DockerHub에서 kindes/node:v1.1.8.0 을 다운로드 받아서 실행한다.

```bash
$ docker ps -a
CONTAINER ID        IMAGE                  COMMAND                  CREATED              STATUS                    PORTS                       NAMES
db3bf5361131        kindest/node:v1.18.0   "/usr/local/bin/entr…"   About a minute ago   Up About a minute         127.0.0.1:32770->6443/tcp   dev-control-plane
```

Local 쿠버네티스 컨텍스트를 확인해 보면 kind-dev라는 이름의 쿠버네티스 컨텍스트를 확인 할 수 있다.

```bash 
$ k config get-contexts
CURRENT   NAME              CLUSTER    AUTHINFO                               NAMESPACE
          k8s               k8s        1f998a63-7764-4bc3-8b71-7f96268b4dda
*         kind-dev          kind-dev   kind-dev
          mgmt-admin@mgmt   mgmt       mgmt-admin
          minikube          minikube   minikube
```


#### Multi Node Cluster
kind 로 쿠버네티스 클러스터를 생성할 때, 설정을 통해서 다양한 설정을 정할 수 있는데, 그중에 하나가 노드의 수를 지정할 수 있다. 

- multi-node-cluster-config.yaml
```yaml
# a cluster with 3 control-plane nodes and 3 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker
```

생성된 config 파일로 쿠버네티스 클러스터를 생성한다.
```bash
$ kind create cluster --name k8s --config kind-example-config-3-3nodes.yaml --image kindest/node:v1.18.0
Creating cluster "k8s" ...
 ✓ Ensuring node image (kindest/node:v1.18.0) 🖼
 ✓ Preparing nodes 📦 📦 📦 📦 📦 📦
 ✓ Configuring the external load balancer ⚖️
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining more control-plane nodes 🎮
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-k8s"
You can now use your cluster with:

kubectl cluster-info --context kind-k8s

Not sure what to do next? 😅 Check out https://kind.sigs.k8s.io/docs/user/quick-start/
```

생성된 클러스터 정보를 확인
```bash
$ kubectl cluster-info --context kind-k8s
Kubernetes master is running at https://127.0.0.1:32771
KubeDNS is running at https://127.0.0.1:32771/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

이전에 생성된 dev 쿠버네티스 클러스터를 확인해보면 로컬호스트의 다른 포트로 API Server가 기동중인것을 확인 할 수 있다.
```bash
$ kubectl --context kind-dev cluster-info
Kubernetes master is running at https://127.0.0.1:32770
KubeDNS is running at https://127.0.0.1:32770/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

기동중인 도커 데몬을 확인한다.
```bash
docker ps -a
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS                    PORTS                       NAMES
24ef23d204f1        kindest/haproxy:2.1.1-alpine   "/docker-entrypoint.…"   10 minutes ago      Up 10 minutes             127.0.0.1:32771->6443/tcp   k8s-external-load-balancer
5723989c3bc8        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes                                         k8s-worker
4780a3feefe3        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes             127.0.0.1:32773->6443/tcp   k8s-control-plane3
1c843d4e2ece        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes                                         k8s-worker2
e791a8b75097        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes                                         k8s-worker3
5cbde9e8ec86        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes             127.0.0.1:32774->6443/tcp   k8s-control-plane
e73995f61160        kindest/node:v1.18.0           "/usr/local/bin/entr…"   10 minutes ago      Up 10 minutes             127.0.0.1:32772->6443/tcp   k8s-control-plane2
db3bf5361131        kindest/node:v1.18.0           "/usr/local/bin/entr…"   23 minutes ago      Up 23 minutes             127.0.0.1:32770->6443/tcp   dev-control-plane
```

