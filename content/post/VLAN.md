---
title :  "VLAN 기본"
date : "2016-10-19T15:46:59+09:00"
categories :
  - "cloud"
tags:
  - "vlan"
  - "network"
  - "network overay"
---

### VLAN (Virtual Local Area Network)
물지적 배치와 상관없이 논리적으로 LAN을 구성할 수 있는 기술
하나의 스위치가 있고 이 스위치에는 포트가 1~10 까지 있다고 하자. 1~5 를 VLAN 1 로 6~10을 VLAN 2 로 설정했다면 물리적으로 같은 스위치에 존재해도 VLAN 1과 VLAN 2는 서로 통신하지 못한다.
스위치의 모든 인터페이스는 동일 브로드캐스트 도메인에 포함되어 있으나 VLAN을 적용할 경우 스위치의 일부 인터페이스를 하나의 브로드캐스트 도메인으로 구성하고, 다른 인터페이스를 또다른 브로드캐스트 도메인으로 구성하여 여러 개의 도메인을 만들수 있습니다. 이렇게 스위치에 의해 논리적으로 만들어진 브로드 캐스트 도메인을 VLAN이라고 합니다.
Access VLAN은 VLAN Tag가 없다. IEEE 802.1Q VLAN Tag 이외에도 VLAN을 구성하는 방식이 있고, 시스템의 ISL과 같은 다른 Tag방식도 있지만


#### VLAN 구성방법
##### 스위치 물리적 포트
스위치의 각 포트별로 VLAN을 할당하는 방법, FastEthernet 0/1,2,3을 VLAN 10, FastEthernet 0/4,5,6을 VLAN20으로 각각의 포트에 다른 VLAN을 설정하는 것
장비에는 어떠한 설정도 해 줄 필요없고, VLAN을 구성하는 포트에 장비를 연결하면 그 장비는 해당 VLAN의 네트워크 그룹에 포함되는 방식
![](http://cfile27.uf.tistory.com/image/24542E3751C98071066E68)

##### MAC Address


##### End-to-End VLANs
VLAN이 전체 스위치(스위치간) 네트워크에 걸쳐져있는 것,만약 사용자가 이동하더라도 user VLAN 멤버쉽은 그대로 유지된다. end-to-end vlan 은 80%의 트래픽은 local workgroup안에서 이루어지고 20%만 remote resource를 목적지로 나가는 상황에서 좋다.
end-to-end VLAN은 설정하기 간편하고 편리한 장점이 있지만 모든 트래픽이 core-layer를 지나가게 되므로 Broadcast Storm 이나 L2 Bridging Loop 와 같은 이유로 CPUI의 리소스를 많이 사용하게되어 Enterprise Network 에서는 권장되지 않습니다.
사용자의 물리적 위치에 상관없이 동일한 VLAN에 묶일수 있습니다.
![](http://cfile7.uf.tistory.com/image/27773D3A51C9808E032AE0)

##### Local VLANs
VLAN 이 각 Access-layer 에 따라서 VLAN이 다르게 지정되는 것을 말합니다.
end-to-end VLAN과 반대로 사용자가 이동하면 다른 VLAN에 묶이게 됩니다.
20%의 트래픽이 Local에서 일어나고 80%의 트래픽이 Remote resource 에서 일어날 때 좋습니다. Local VLAN을 사용하면 처음 생성과 관리에는 불편하지만 장애 발생시 장애 요인을 찾기가 쉽습니다.
![](http://cfile30.uf.tistory.com/image/2245983351C9809B3258A8)

#### VTP (VLAN Trunking Protocol)
복수개의 스위치들이 VLAN 설정 정보를 교환할 때 사용하는 프로토콜

#### Trunking (트렁킹)
각기 다른 VLAN이 데이터를 주고 받을수 있께 한 라인으로 된 통로이다. VLAN이 N개 일때, 스위치가 링크는 N개여야 한다. Trunking은 VLAN이 N개여도 하나의 통합링크를 통해서 패킷을 보내는 방식이다. 하나의 통합 링크를 통해 보내므로 각 패킷이 포함된 VLAN정보를 알수 없습니다.

![](http://cfile5.uf.tistory.com/image/036C1E3D51CA50C314DBC6)




참고 : [Flatimum 블로그 : Virtual LAN VLAN 개념 및 설정](http://onecellboy.tistory.com/278)
