---
title: "Istio 1.5로 구성하는 서비스메시"
date: 2020-04-03T00:00:00+00:00
tags: ["istio", "kubernetes", "Service Mesh"]
---

# Istio로 구성하는 서비스 메시

## 이전 Architecture
일반적으로 Istio의 구성요소라고 하면, 아래와 같이 이야기한다.
- Envoy Proxy
- Mixer
- Pilot
- Galley
- Citadel

![](/img/istio/istio_old_architecture.png)

하지만, Mixer로 인한 네트워크 레이턴시 성능 이슈가 있어서, 아키텍처가 변경이 되었는데 Mixer가 없어지고 Telemetry로 변경이 되고, Envoy Proxy 에서 바로 모니터링 솔루션우로 메트릭을 전송하는 형태로 변경이 되었다.

## 달라진 Architecture
![](/img/istio/istio_architecutre.png)

