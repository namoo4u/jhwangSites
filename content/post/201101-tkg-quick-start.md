---
title: "TKG 1.2 설치/설정 : quick start"
date: 2020-11-01T00:00:00+00:00
tags: ["kubernetes", "tkg", "tanzu"]
---

## Storage Class
```
k delete sc default

k apply -f -<<-EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: default
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: csi.vsphere.vmware.com
parameters:
  storagepolicyname: "TKG Storage Policy"     # optional
EOF
```



## MetalLB 
```
k apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/namespace.yaml
k apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/metallb.yaml
k create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"

k apply -f -<<-EOF
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
      - 192.168.0.140-192.168.0.150
EOF
```


## Cert Manager
```
cd tkg-extensions-v1.2.0+vmware.1
kubectl apply -f cert-manager/
kubectl apply -f extensions/tmc-extension-manager.yaml
kubectl apply -f extensions/kapp-controller.yaml
```

## Contour
```
cd extensions/ingress/contour
kubectl apply -f namespace-role.yaml

cat > vsphere/contour-data-values.yaml <<EOF
#@data/values
#@overlay/match-child-defaults missing_ok=True
---
infrastructure_provider: "vsphere"
contour:
  image:
    repository: registry.tkg.vmware.run
envoy:
  image:
    repository: registry.tkg.vmware.run
  service:
    type: LoadBalancer
EOF

kubectl create secret generic contour-data-values --from-file=values.yaml=vsphere/contour-data-values.yaml -n tanzu-system-ingress
kubectl apply -f contour-extension.yaml
```

## Metrics Server 
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.0/components.yaml
```
혹 x509 error 와 같은 에러가  발생하면
```
command:
- /metrics-server
- --kubelet-insecure-tls
```

commnad 에 kubelet-insecure-tls 를 추가해 준다. 


## Harbor
```
helm install harbor harbor/harbor --set expose.ingress.hosts.core=harbor.vsphere.skt --set expose.ingress.hosts.notary=notary.vsphere.skt --set externalURL=https://harbor.vsphere.skt:32588 -n harbor --set persistence.persistentVolumeClaim.registry.size=100Gi
```

helm install harbor harbor/harbor --set expose.ingress.hosts.core=harbor-02.vsphere.skt --set expose.ingress.hosts.notary=notary-02.vsphere.skt --set externalURL=https://harbor-02.vsphere.skt -n tanzu-system-harbor --set persistence.persistentVolumeClaim.registry.size=100Gi



## Istioctl 
특정버전의 istioctl 다운로드 하기
```
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.1.9 sh -
```

## Prometheus / Grafana
```
k create ns tanzu-system-monitoring

helm repo add prometheus https://prometheus-community.github.io/helm-charts
helm repo update

helm uninstall prometheus -n tanzu-system-monitoring
helm install prometheus prometheus/kube-prometheus-stack -n tanzu-system-monitoring --set grafana.service.type=LoadBalancer
```

## EFK
```
k create ns tanzu-system-logging

helm repo add stable https://charts.helm.sh/stable
helm repo add elastic https://helm.elastic.co
helm repo update

helm uninstall elasticsearch -n tanzu-system-logging
helm install elasticsearch elastic/elasticsearch -n tanzu-system-logging

helm uninstall fluent-bit -n tanzu-system-logging
helm install fluent-bit stable/fluent-bit -n tanzu-system-logging --set backend.type=es --set backend.es.host=elasticsearch-master

helm uninstall kibana -n tanzu-system-logging
helm install kibana elastic/kibana -n tanzu-system-logging --set service.type=LoadBalancer --set service.port=80
```

## Gitlab CE

```
helm repo add gitlab https://charts.gitlab.io/
helm repo update

kubectl create ns tanzu-system-gitlab
helm -n tanzu-system-gitlab install gitlab gitlab/gitlab \
  --set certmanager.install=false \
  --set certmanager-issuer.email=jupil.hwang@gmail.com \
  --set gitlab.gitlab-shell.minReplicas=1 \
  --set gitlab.gitlab-shell.maxReplicas=1 \
  --set gitlab.sidekiq.minReplicas=1 \
  --set gitlab.sidekiq.maxReplicas=1 \
  --set gitlab.webservice.minReplicas=1 \
  --set gitlab.webserivce.maxReplicas=1 \
  --set gitlab.task-runner.enabled=false \
  --set gitlab-runner.install=false \  
  --set global.hosts.domain=haas-411.pez.vmware.com \
  --set global.hosts.externalIP= \
  --set global.ingress.annotations.cert-manager\.io\/cluster-issuer=letsencrypt-contour-cluter-issuer \
  --set global.ingress.annotations.kubernetes\.io\/tls-acme=true \
  --set global.projectcontour\.io\/ingress.class=contour \
  --set externalUrl=gitlab.haas-411.pez.vmware.com \
  --set ngnix-ingress.enabled=false \
  --set prometheus.install=false \
  --set global.edition=ce 

kubectl -n tanzu-system-gitlab get secret gitlab-gitlab-initial-root-password -ojsonpath='{.data.password}' | base64 --decode ; echo
```



## vMLP
vMLP 는 VMware 인프라위에 Machine Learning 워크로드를 효과적으로 실행하기 위한 Data Scientists 를 위한 ML Platform 이다.

- VMware 와 Kubernetes 위에 가상화된 환경 제공
- Kubeflow 와 Horovod 기반의 분샨 모델 트레이닝과 친숙한 Jupyter notebook 제공
- TKG(Tanzu Kubernetes Grid) 기반에서 GPU 지원 : [vGPU](https://blogs.vmware.com/apps/2018/09/using-gpus-with-virtual-machines-on-vsphere-part-3-installing-the-nvidia-grid-technology.html) / [NVIDIA Kubernetes Device Plugin](https://github.com/NVIDIA/k8s-device-plugin)
- 기본 제공 배포 프레임워크 기반의 모델 테스에 대한 빠른 처리


```
kubectl get svc -n istio-system istio-ingressgateway
```

- kubeflow UI --> http://<endpoint> 
- vMLP (e.g. Data Manager) UI ---> http://<endpoint>/vmlp/
- MLflow UI ---> http://<endpoint>/mlflow/

admin@kubeflow.org / 12341234 