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
  ![](/img/kubesphere/pks-cluster-create.png)
- 클러스터 생성 확인
  ```bash
  pks cluster k8s
  ```
  ![](/img/kubesphere/pks-cluster-create-02.png)
  - bosh cli로 생성된 K8s 클러스터 VM 확인 
  ![](/img/kubesphere/pks-cluster-create-03.png)
- DNS Setting for K8s Master API LB
생성된 클러스터의 Master API Server의 IP 주소를 DNS에 클러스터 생성 시 입력한 external-hostname으로 등록한다.
![](/img/kubesphere/k8s-api-dns.png)

- EPMC : Enterprise PKS
  - summay
![](/img/kubesphere/epmc-k8s-cluster-summary.png)
  - ndoes
![](/img/kubesphere/epmc-k8s-cluster-nodes.png)
  - namespaces
![](/img/kubesphere/epmc-k8s-cluster-namespaces.png)

- Get Kubernetes Credentials for Kubectl cli
  ```bash
  pks get-credentials k8s
  ```
  - kubectl cli 확인
  ```bash
  kubectl cluster-info
  kubectl api-resources
  ```
  ![](/img/kubesphere/kubectl-cluster-info.png)
  ![](/img/kubesphere/kubectl-api-resources.png)
# Software
- Helm
- Kubesphere

## Default Storage Class 생성

- Storage Class 생성
  ```yaml
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
  ```yaml
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

- helm 에서 사용할 repo 추가 및 업데이트하기
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
  ![](/img/kubesphere/kubesphere-install-log.png)

- Browsing kubesphere web ui
  ![](/img/kubesphere/kubesphere-web-ui.png)

  - Logging 부분의 Elastic Search가 설치되어 있지 않고, Istio 와 Monitoring 에서 실행되지 않고 에러가 나는 부분이 있다. 
  ks-installer 컨피그맵을 수정하여 추가적으로 설치한다.
    ```bash
    kubectl edit cm ks-installer -n kubepshere-system
    ```
    ![](/img/kubesphere/ks-web-ui-02.png)

    ** node exporter 에 오류가 있는것은 나중에 확인 필요하다 **


# Deploy Sample Application

## Create Workspace / Project
- workspace 생성
![](/img/kubesphere/ks-workspace.png)

- project 생성
  - project는 쿠버네티스의 네임스페이스로 생성된다.
  ![](/img/kubesphere/ks-project.png)

  - 네임스페이스의 리소스 쿼터를 지정한다.
  ![](/img/kubesphere/ks-project-resource-quota.png)


## Nginx Web Application
- kubesphere > App Store > Nginx > Deploy
  ![](/img/kubesphere/nginx-deploy.png)

- 디플로이먼트 확인
  ![](/img/kubesphere/nginx-deployment.png)

- 서비스 확인
  ![](/img/kubesphere/nginx-services.png)

- 웹 브라우저 확인
  ![](/img/kubesphere/nginx-web-browser.png)
  ** 파드 IP가 NSX-T에서 Routable 로 설정되어 있어서 Windows Jumpbox 에서 바로 엑세스가 가능하다. Routable 이 아닌경우 서비스를 NodePort / LoadBalancer / Ingress 가 필요하다 **

## GitLab CE
- VCS 로 git server를 설치한다.
  ```bash
  helm install --name gitlab --namespaces git stable/gitlab-ce --set externalUrl=http://git.k8s.kdis.local,gitlabRootPassword=VMware1!
  ```
  ![](/img/kubesphere/gitlab-ce-install.png)
- gitlab running 확인
  ![](/img/kubesphere/gitlab-ce-running.png)

- dns setting
  helm 설치시 설정으로 external URL을 git.k8s.kdis.local 로 했기 때문에, DNS에 LoadBalancer External IP를 지정해 준다.

- Browser Access 후 Project Git Repo 생성
  ![](/img/kubesphere/gitlab-ce-project-repo.png)

  **http://git.k8s.kdis.local/root/hello-card.git**

## Create Spring Boot Application and Containerize
- start.spring.io 로 sample download
  ```bash
  spring init --build gradle --java-version=1.8  --dependencies=web,thymeleaf,actuator
  ```

- add WelcomeController to DemoApplication.java
  ```java
  @Controller
  class WelcomeController {
    @GetMapping("/")
    public String main(Model model)	{
      return "welcome";
    }
  }
  ```
- add welcome.html to resources/templates folder
  ```html
  <body bgcolor="lightgreen">
  <h1>
      Hello World
  </h1>
  </body>
  ```


- Run Sample Application 
  ```bash
  ./gradlew bootRun
  ```

- Push to Git Repo
윈도우즈에 git 에 설치되어 있지 않은 경우, git 을 다운로드해서 설치한다.

- ~~Containerize with Docker and Dockerfile~~

- Containerize with Jib
  - build.gradle > plugins 에 아래 추가
  ```groovy
  plugins {
    id("com.google.cloud.tools.jib") version "2.1.0"
  }
  ```
  - Jib 설정 추가
  ```groovy
  jib {
    to {
      //image="10.195.70.131:443/biz-a-team/simple-app"
      image="jhwangdemo/simple-app"
      auth {
        username="jhwangdemo"
        password="P@ssw0rd1234"
      }
    }
    from {
      image="openjdk:8-alpine"
    }
    allowInsecureRegistries(true)
  }
  ```

<!-- ## Image Builder
- Image Builder를 생성한다
  ![](/img/kubesphere/image-builder-create.png)

- Git Repo 에 대한 Secret 와 Target image repository 가 필요하다 -->


