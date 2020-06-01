---
title: "vSphere with Kubernetes의 Master/Worker Node SSH 접속하기"
date: 2020-05-04T00:00:00+00:00
tags: ["vsphere", "kubernetes", "k8s", "tanzu"]
---

# vSphere with Kubernetes

## vSphere 7.0
vSphere 7.0의 가장 큰 차이라고 한다면 당연하게 Kubernetes의 지원이다. 기존에 가상 머신, 스토리지, 네트워크의 SDDC부분에 중점을 두었다면, 쿠버네티스를 사용한 메니지먼트로 다양한 환경에서 동일한 메니페스트를 통해 관리를 할 수 있도록 한 것이다. 
![](/img/tanzu/vsphere_with_kubernetes_0505.png)

## Supervisor Cluster
vSphere 7.0 의 Supervisor 클러스터를 통해서 vSphere 전반적인 가상머신, vSphere Pod, Tanzu Kubernetes Cluster를 생성/삭제 등의 라이프사이클을 관리한다. 사용자/그룹 관리, SSO연계, 퍼미션 설정, 네임스페이스 관리 등을 한다.

### Supervisor 노드 접속
Supervisor노드에 접속하기 위해서
- Supervisor 클러스터(or Workload Cluster Provider) 로그인
kubectl-vsphere 플러그인 설치하고 로그인을 한다.
```bash
kubectl vsphere login --insecure-skip-tls-verify --server <wcp.server.uri> -u administrator@vsphere.local
```
- Supervisor Nodes
```bash
kubectl get node -o wide
```
![](/img/tanzu/vsphere_supervior_nodes.png)

Supervisor Cluster를 보면 Master와 Agent라는 두가지 롤의 노드가 있다. 그리고 Agent Node는 이름에서 볼 수 있듯이, ESXi Node 자체가 Agent 인것을 알 수 있다. ESXi Node에는 Virtual Kubelet을 수정해서 만든 Spherelet과 ContainerD와 같은 역할을 하는 HostD 라는 Agent가 동작하며, Agent의 Api Server와 Etcd, Scheduler, Controller Manager등이 동작하고 있는 Master 노드가 있다.

- Supervisor Worker 노드(Agent 노드) 접속
![](/img/tanzu/vsphere_supervisor_agent_node_ssh.png)

- Supervisor Master 노드 접속
Master Node에 접속하기 위해서는 root 계정의 패스워드를 알아야 하는데, vCenter VM에 root 접속하여 패스워드를 알수 있다.
```bash
$ ssh root@<vsphere_client_vm>
command > shell
# /usr/lib/vmware-wcp/decryptK8Pwd.py
```
![](/img/tanzu/vsphere_supervisor_get_root_password.png)

![](/img/tanzu/vsphere_supervisor_master_ssh.png)

## Tanzu Kubernetes Cluster

Tanzu kuberenetes Cluster (aka Guest Cluster)에 ssh 접속하기 위한 패스워드는 Supervisor Cluster의 Secrets 에 정보가 있다.

```bash
kubectl -n ns1 get secrets
NAME                              TYPE                                  DATA   AGE
default-token-22q9s               kubernetes.io/service-account-token   3      6d10h
ns1-default-image-pull-secret     kubernetes.io/dockerconfigjson        1      6d10h
ns1-default-image-push-secret     kubernetes.io/dockerconfigjson        1      6d10h
tkg-cluster-1-ca                  Opaque                                2      6d4h
tkg-cluster-1-ccm-token-vwwlq     kubernetes.io/service-account-token   3      6d4h
tkg-cluster-1-encryption          Opaque                                1      6d4h
tkg-cluster-1-etcd                Opaque                                2      6d4h
tkg-cluster-1-kubeconfig          Opaque                                1      6d4h
tkg-cluster-1-proxy               Opaque                                2      6d4h
tkg-cluster-1-pvcsi-token-knkqb   kubernetes.io/service-account-token   3      6d4h
tkg-cluster-1-sa                  Opaque                                2      6d4h
tkg-cluster-1-ssh                 kubernetes.io/ssh-auth                1      6d4h
tkg-cluster-1-ssh-password        Opaque                                1      6d4h
```
![](/img/tanzu/vsphere_supervisor_cluster_secretss.png)

- tkg-cluster-1-ssh 는 Private.key 이고 tkg-cluster-1-ssh-password는 암호가 들어 있다.
```bash
kubectl -n ns1 get secrets tkg-cluster-1-ssh -o jsonpath='{.data.ssh-privatekey}' > tkg-cluster-1-ssh-private.key
```

```bash
k -n ns1 get secrets tkg-cluster-1-ssh-password -o jsonpath='{.data.ssh-passwordkey}'  | base64 -D
```
   리눅스에서는 base64 -d, Mac에서는 base64 -D 로 사용한다

- Tanzu kubernetes cluster의 노드에 ssh 접속하기
```bash
ssh -i tkg-cluster-1-ssh-private.key root@<tkg-clsuter-node-ip>
```
하지만, Supervisor Master Node 와 다르게, Tanzu Kubernetes Cluster의 Master Node는 사설 IP만을 가지고 있어서, Local 에서 바로 접속할 수가 없다. 따라서 중간에서 Bridge(Proxy)를 해줄 것이 필요하다. Supervisor Cluster에 Jumpbox Pod를 실행한다.

```yaml
apiVersion: v1
kind: Pod
metadata:  
  name: jumpbox
  namespace: ns1
spec:  
  containers:  
  - image: "photon:3.0"
    name: jumpbox
    command: [ "/bin/bash", "-c", "--" ]
    args: [ "yum install -y openssh-server; mkdir /root/.ssh; cp /root/ssh/ssh-privatekey /root/.ssh/id_rsa; chmod 600 /root/.ssh/id_rsa; while true; do sleep 30; done;" ]
    volumeMounts:
    - mountPath: "/root/ssh"
      name: ssh-key
      readOnly: true
  volumes:
  - name: ssh-key
    secret:
      secretName: tkg-cluster-ssh
```

![](/img/tanzu/vsphere_supervisor_jumpbox_pod.png)

jumpbox pod이 실행이 되었으면, jumpbox를 통해 Tanzu Kubernetes Cluster에 접속한다.
- 먼저 node의 IP를 확인한다

```bash
kubectl config use-context tkg-cluster-1
kubectl get no -o wide
```
![](/img/tanzu/vsphere_suervisor_tkg_nodes.png)

또는

Supervisor Cluster에서 virtualmachine의 vmIP를 확인

```bash
kubectl -n ns1 get virtualmachine tkg-cluster-1-workers-4qnlq-54c68bbb84-8w6jh -o jsonpath={.status.vmIp}
```
![](/img/tanzu/vsphere_supervisor_tkg_node_virtualmachine_vmIp.png)

- jumpbox의 ssh 를 실행해서 접속한다
```bash
kubectl -n ns1 exec -it jumpbox -- ssh vmware-system-user@<node_vmIp>
```

![](/img/tanzu/vsphere_supervisor_ssh_tkg_node_2.png)