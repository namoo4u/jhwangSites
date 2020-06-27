---
title: "TKG에 MetalLB"
date: 2020-06-20T00:00:00+00:00
tags: ["kubernetes", "lb", "LoadBalancer", "metallb"]
---

# MetalLB

```bash
# kubectl config use-context my-cluster-admin@my-cluster
 
# kubectl create ns metallb-system
 
# kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.9.3/manifests/metallb.yaml -n metallb-system
podsecuritypolicy.policy/controller created
podsecuritypolicy.policy/speaker created
serviceaccount/controller created
serviceaccount/speaker created
clusterrole.rbac.authorization.k8s.io/metallb-system:controller created
clusterrole.rbac.authorization.k8s.io/metallb-system:speaker created
role.rbac.authorization.k8s.io/config-watcher created
role.rbac.authorization.k8s.io/pod-lister created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:controller created
clusterrolebinding.rbac.authorization.k8s.io/metallb-system:speaker created
rolebinding.rbac.authorization.k8s.io/config-watcher created
rolebinding.rbac.authorization.k8s.io/pod-lister created
daemonset.apps/speaker created
deployment.apps/controller created
 
# kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
secret/memberlist created
 
# cat /tkg/metallb-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 10.193.147.200-10.193.147.250
 
# kubectl apply -f tkg/metallb-configmap.yaml
configmap/config created
 
# kubectl get all -n metallb-system
NAME                              READY   STATUS    RESTARTS   AGE
pod/controller-7fb45985f9-68kxf   1/1     Running   0          29s
pod/speaker-9kkk9                 1/1     Running   0          29s
pod/speaker-hx5xh                 1/1     Running   0          29s
pod/speaker-w4xbf                 1/1     Running   0          29s
pod/speaker-wcghj                 1/1     Running   0          29s
 
NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
daemonset.apps/speaker   4         4         4       4            4           beta.kubernetes.io/os=linux   29s
 
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/controller   1/1     1            1           29s
 
NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/controller-7fb45985f9   1         1         1       29s
```

# Test with simpel echo-server
```bash
# kubectl create deployment echo --image=inanimate/echo-server
deployment.apps/echo created
 
# kubectl scale deployment echo --replicas=3
deployment.apps/echo scaled
 
# kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
echo   3/3     3            3           24s
 
# kubectl expose deployment echo --port=8080 --type LoadBalancer
service/echo exposed
 
# curl http://192.168.100.50:8080/
Welcome to echo-server!  Here's what I know.
  > Head to /ws for interactive websocket echo!
 
-> My hostname is: echo-7c9fd74899-9rtmk
 
-> Requesting IP: 192.168.100.128:9292
 
-> Request Headers |
 
  HTTP/1.1 GET /
 
  Host: 192.168.100.50:8080
  Accept: */*
  User-Agent: curl/7.58.0
 
-> Response Headers |
 
  Content-Type: text/plain
  X-Real-Server: echo-server
 
  > Note that you may also see "Transfer-Encoding" and "Date"!
 
-> My environment |
  ADD_HEADERS={"X-Real-Server": "echo-server"}
  HOME=/
  HOSTNAME=echo-7c9fd74899-9rtmk
  KUBERNETES_PORT=tcp://100.64.0.1:443
  KUBERNETES_PORT_443_TCP=tcp://100.64.0.1:443
  KUBERNETES_PORT_443_TCP_ADDR=100.64.0.1
  KUBERNETES_PORT_443_TCP_PORT=443
  KUBERNETES_PORT_443_TCP_PROTO=tcp
  KUBERNETES_SERVICE_HOST=100.64.0.1
  KUBERNETES_SERVICE_PORT=443
  KUBERNETES_SERVICE_PORT_HTTPS=443
  PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
  PORT=8080
  SSLPORT=8443
 
-> Contents of /etc/resolv.conf |
search default.svc.cluster.local svc.cluster.local cluster.local
nameserver 100.64.0.10
options ndots:5
 
-> Contents of /etc/hosts |
# Kubernetes-managed hosts file.
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
fe00::0 ip6-mcastprefix
fe00::1 ip6-allnodes
fe00::2 ip6-allrouters
100.120.133.66  echo-7c9fd74899-9rtmk
 
-> And that's the way it is 2020-04-06 21:24:03.617944335 +0000 UTC
 
// Thanks for using echo-server, a project by Mario Loria (InAnimaTe).
// https://github.com/inanimate/echo-server
// https://hub.docker.com/r/inanimate/echo-server
```