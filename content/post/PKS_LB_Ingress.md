---
title: "PKS의 NSX에서 LB와 Ingress 사용"
date: 2020-04-23T00:00:00+00:00
draft: true
tags: ["PKS", "NSX-T", "Ingress"]
---

# PKS와 네트워크 - PKS의 NSX에서 LB와 Ingress 사용
## Network Profile
쿠버네티스 클러스터에서 사용할 네트워크를 위해 미리 프로파일을 생성해 놓고 PKS에서 클러스터를 만들 때 이 네트워크 프로파일을 지정할 수 있다.

- 기본 쿠버네티스 클러스터 속성
```bash
ubuntu@ubuntu-205:~/works/pks-network-profile$ pks cluster k8s --details

PKS Version:              1.7.0-build.26
Name:                     k8s
K8s Version:              1.16.7
Plan Name:                Small
UUID:                     a09dc43b-c1dc-42db-9fad-c00c2d3594f0
Last Action:              CREATE
Last Action State:        succeeded
Last Action Description:  Instance provisioning completed
Kubernetes Master Host:   10.195.70.136
Kubernetes Master Port:   8443
Worker Nodes:             2
Kubernetes Master IP(s):  10.195.70.136
Network Profile Name:
Kubernetes Profile Name:
Tags:

NSXT Network Details:
  Load Balancer Size                       (lb_size):                  "small"
  Nodes DNS Setting                        (nodes_dns):                ["10.192.2.10","10.192.2.11"]
  Node IP addresses are routable [no-nat]  (node_routable):            false
  Nodes subnet prefix                      (node_subnet_prefix):       24
  POD IP addresses are routable [no-nat]   (pod_routable):             false
  PODs subnet prefix                       (pod_subnet_prefix):        24
  NS Group ID of master VMs                (master_vms_nsgroup_id):    ""
  Tier 0 Router identifier                 (t0_router_id):             "a85c1b46-653d-459a-97f7-734d7d214e5c"
  Floating IP Pool identifiers             (fip_pool_ids):             ["c232499d-6de5-45e2-bf72-c769c3fcd787"]
  Node IP block identifiers                (node_ip_block_ids):        ["ba66ad9a-b7f1-417b-91d0-bbe64cdc2c12"]
  POD IP block identifiers                 (pod_ip_block_ids):         ["e8136240-90b2-45ba-a30e-0f6d77b077b4"]
  Shared tier1                             (single_tier_topology):     true
  Infrastructure networks                  (infrastructure_networks):  ""
Kubernetes Settings Details:
  Set by Plan:
  Kubelet Node Drain timeout (mins)          (kubelet-drain-timeout):            0
  Kubelet Node Drain grace-period (seconds)  (kubelet-drain-grace-period):       10
  Kubelet Node Drain force                   (kubelet-drain-force):              true
  Kubelet Node Drain force-node              (kubelet-drain-force-node):         false
  Kubelet Node Drain ignore-daemonsets       (kubelet-drain-ignore-daemonsets):  true
  Kubelet Node Drain delete-local-data       (kubelet-drain-delete-local-data):  true
```


lb_size : small
single_tier_topology: true
node_routable: false
pod_routable: false


PKS에서는 기본으로 멀티 마스터를 위한 LB와 Ingress를 위한 LB의 Virtual Server가 만들어지는데, Network Profile 에서 이를 비활성할 수 있다.

- disable_lb_ingress.yaml
```yaml
{
  "name": "lb_ingress_disable",
  "description": "nsx-t ingress controller: false, nsx-lb: false",
  "parameters" : {
     "cni_configurations": {
         "type": "nsxt",
         "parameters": {
            "nsx_lb": false,
            "nsx_ingress_controller": false
         }
     }
  }
}
```

- 쿠버네티스 network-profile 생성
```bash
pks create-network-profile disable_lb_ingress.yaml
```

T1 Router 에서 NAT 서비스로 SNAT가 각 Node IP Pool 과 Pod IP Pool 에 맴핑됨
![](/img/pks_nsx-t_network-profile/t1_servics_NAT.png)

기본 네트워크 프로파일로 클러스터를 생성시, Master 노드의 API Server 용 로드밸런서 Virtual Server와 NSX-T Ingress Controller를 위한 로드밸런서 Virtual Server 가 생성됨
![](/img/pks_nsx-t_network-profile/lb_true_ingress_true_virtualserver.png)

lb_ingress_disable 네트워크 프로파일을 가지고 생성된 쿠버네티스 클러스터의 Master API Server용 NSX-T 로드밸런서 하나만 생성됨
![](/img/pks_nsx-t_network-profile/lb_false_ingress_false_virtual_servers.png)


Pod routable: true, nsx_ingress_controller: false
```yaml
{
  "name": "pod_routable",
  "description": "nsx-t ingress controller: false, nsx-lb: false",
  "parameters" : {
     "cni_configurations": {
         "type": "nsxt",
         "parameters": {
            "pod_routable": true,
            "node_routable": true,
            "nsx_ingress_controller": false
         }
     }
  }
}
```

