---
title: "HAProxy - dataplaneapi"
date: 2020-06-30T00:00:00+00:00
tags: ["tkg", "loadbalancer", "dataplaneapi", "haproxy"]
---

# HA-Proxy
SW loadbalancer로 제일 많이 사용하는 것 중에 한가지가 nginx나 haproxy 일것이다. http/s 위주의 서비스라면 nginx로 충분하겠지만, 범용으로 tcp/udp 서비스를 위한 Load Balancer 로 사용할려면 ha-proxy 를 사용하는 것이 좋다. 
또는 envoy나 traefik 등을 사용할 수 있다.

subject | http | https
---: | --- | ---
Latency (HTTP) | ![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-1.png) | ![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-2.png)
Request/Second | ![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-3.png) | ![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-4.png)

출처 : https://www.loggly.com/blog/benchmarking-5-popular-load-balancers-nginx-haproxy-envoy-traefik-and-alb/

위의 Benchmark 자료를 보면 알 수 있듯이, envoy나 traefik이 요즘 유행하고 있지만, 역시 구관이 명관이다. 

# 설정 reload
## haproxy.cfg
- /etc/haproxy/haproxy.cfg 에 haproxy 설정을 추가하고 변경된 설정을 적용하기 위해서 haproxy reload 를 해줘야 하는데, 설정이 자주 변경될 때 매번 haproxy 서버에 접속해서 haproxy.cfg 를 변경하고 haproxy 서버를 재 설정해 줘야 한다.

## dataplane api 
- haproxy enterprise 를 사용하면 haproxy-dataplane-api 패키지를 사용하면 되지만, haproxy-key 가 필요하다. 
- 하지만, dataplane-api 는 oss 로 github에서 다운로드하여 사용할 수 있다.

```bash
# git clone
git clone https://github.com/haproxytech/dataplaneapi.git

# build
make build
```

# 예
## 
```bash
./dataplaneapi --port 5555 -b /usr/sbin/haproxy -c /etc/haproxy/haproxy.cfg -d 5 -r "service haproxy reload" -s "service haproxy restart" -u dataplaneapi -t /tmp/haproxy
```
```
curl -u <user>:<pass> -H "Content-Type: application/json" "http://127.0.0.1:5555/v2/"
```



