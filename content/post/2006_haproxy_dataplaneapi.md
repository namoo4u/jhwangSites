---
title: "HAProxy - dataplaneapi"
date: 2020-06-30T00:00:00+00:00
tags: ["tkg", "loadbalancer", "dataplaneapi", "haproxy"]
---

# HA-Proxy
SW loadbalancer로 제일 많이 사용하는 것 중에 한가지가 nginx나 haproxy 일것이다. http/s 위주의 서비스라면 nginx로 충분하겠지만, 범용으로 tcp/udp 서비스를 위한 Load Balancer 로 사용할려면 ha-proxy 를 사용하는 것이 좋다. 
또는 envoy나 traefik 등을 사용할 수 있다.

![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-1.png)
![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-2.png)
![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-3.png)
![](/img/tanzu/haproxy/Benchmarking-5-Popular-Load-Balancers-4.png)
출처 : https://www.loggly.com/blog/benchmarking-5-popular-load-balancers-nginx-haproxy-envoy-traefik-and-alb/

위의 Benchmark 자료를 보면 알 수 있듯이, envoy나 traefik이 요즘 유행하고 있지만, 역시 구관이 명관이다. 

