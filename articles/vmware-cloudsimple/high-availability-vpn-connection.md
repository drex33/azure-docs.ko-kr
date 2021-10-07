---
title: Azure VMware Solution by CloudSimple - 온-프레미스에서 CloudSimple VPN 게이트웨이로의 고가용성 구성
description: 온-프레미스 환경에서 고가용성을 위해 사용하도록 설정된 CloudSimple VPN 게이트웨이로 고가용성 연결을 구성하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e0ccebc3f60ad8a83ea787ef77113d296301845e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612031"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>온-프레미스에서 CloudSimple VPN 게이트웨이로의 고가용성 연결 구성

네트워크 관리자는 온-프레미스 환경에서 CloudSimple VPN 게이트웨이로의 고가용성 IPsec 사이트 간 VPN 연결을 구성할 수 있습니다.

이 가이드에서는 IPsec 사이트 간 VPN 고가용성 연결을 위해 온-프레미스 방화벽을 구성하는 단계를 안내합니다. 세부 단계는 온-프레미스 방화벽의 형식에 따라 다릅니다. 예를 들어 이 가이드에서는 Cisco ASA와 Palo Alto Networks의 두 가지 방화벽 형식에 대한 단계를 안내합니다.

## <a name="before-you-begin"></a>시작하기 전에

온-프레미스 방화벽을 구성하기 전에 다음 작업을 수행합니다.

1. 조직에서 필요한 노드를 [프로비전](create-nodes.md)하고 하나 이상의 CloudSimple 프라이빗 클라우드를 만들었는지 확인합니다.
2. 온-프레미스 네트워크와 CloudSimple 프라이빗 클라우드 간에 [사이트 간 VPN 게이트웨이를 구성](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)합니다.

지원되는 1단계 및 2단계 제안은 [VPN 게이트웨이 개요](cloudsimple-vpn-gateways.md)를 참조하세요.

## <a name="configure-on-premises-cisco-asa-firewall"></a>온-프레미스 Cisco ASA 방화벽 구성

이 섹션의 지침은 Cisco ASA 버전 8.4 이상에 적용됩니다. 구성 예에서는 Cisco Adaptive Security Appliance 소프트웨어 버전 9.10이 배포되고 IKEv1 모드로 구성되어 있습니다.

사이트 간 VPN이 작동하려면 온-프레미스 Cisco ASA VPN 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP(피어 IP)의 UDP 500/4500 및 ESP(IP 프로토콜 50)를 허용해야 합니다.

### <a name="1-configure-phase-1-ikev1"></a>1. 1단계 구성(IKEv1)

외부 인터페이스에서 1단계(IKEv1)를 사용하도록 설정하려면 Cisco ASA 방화벽에서 다음 CLI 명령을 입력합니다.

`crypto ikev1 enable outside`

### <a name="2-create-an-ikev1-policy"></a>2. IKEv1 정책 만들기

해시, 인증, Diffie-Hellman 그룹, 수명 및 암호화에 사용할 알고리즘과 메서드를 정의하는 IKEv1 정책을 만듭니다.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 터널 그룹 만들기

IPsec 특성 아래에 터널 그룹을 만듭니다. [사이트 간 VPN 게이트웨이 구성](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) 시 설정한 피어 IP 주소와 터널 미리 공유한 키를 구성합니다.

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 2단계 구성(IPsec)

2단계(IPsec)를 구성하려면 암호화 및 터널링할 트래픽을 정의하는 ACL(액세스 제어 목록)을 만듭니다. 다음 예에서 관심 있는 트래픽은 온-프레미스 로컬 서브넷(10.16.1.0/24)이 원본인 터널에서 프라이빗 클라우드 원격 서브넷(192.168.0.0/24)까지입니다. 사이트 사이에 여러 서브넷이 있는 경우 ACL에 여러 항목이 포함될 수 있습니다.

Cisco ASA 버전 8.4 이상에서는 네트워크, 서브넷, 호스트 IP 주소 또는 여러 개체에 대한 컨테이너 역할을 하는 개체 또는 개체 그룹을 생성할 수 있습니다. 로컬에 대한 개체와 원격 서브넷에 대한 개체를 만들고 암호화 ACL 및 NAT 문에 사용합니다.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>온-프레미스 로컬 서브넷을 개체로 정의

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>CloudSimple 원격 서브넷을 개체로 정의

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>관심 있는 트래픽에 대한 액세스 목록 구성

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 변환 집합 구성

키워드 ```ikev1```과 관련된 TS(변환 집합)을 구성합니다. TS에 지정된 암호화 및 해시 특성은 [CloudSimple VPN 게이트웨이에 대한 기본 구성](cloudsimple-vpn-gateways.md)에 나열된 매개 변수와 일치해야 합니다.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 암호화 맵 구성

다음 구성 요소를 포함하는 암호화 맵을 구성합니다.

* 피어 IP 주소
* 관심 있는 트래픽을 포함하는 정의된 ACL
* 변환 집합

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 암호화 맵 적용

외부 인터페이스에 암호화 맵을 적용합니다.

`crypto map mymap interface outside`

### <a name="8-confirm-applicable-nat-rules"></a>8. 적용 가능한 NAT 규칙 확인

다음은 사용되는 NAT 규칙입니다. VPN 트래픽에 다른 NAT 규칙이 적용되지 않는지 확인합니다.

`nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside`

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Cisco ASA의 샘플 IPsec 사이트 간 VPN 설정 출력

1단계 출력:

![Cisco ASA 방화벽에 대한 1단계 출력](media/ha-vpn-connection-cisco-phase1.png)

2단계 출력:

![Cisco ASA 방화벽에 대한 2단계 출력](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>온-프레미스 Palo Alto Networks 방화벽 구성

이 섹션의 지침은 Palo Alto Networks 버전 7.1 이상에 적용됩니다. 이 구성 예에서는 Palo Alto Networks VM-Series 소프트웨어 버전 8.1.0이 배포되고 IKEv1 모드로 구성되어 있습니다.

사이트 간 VPN이 작동하려면 온-프레미스 Palo Alto Networks 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP(피어 IP)의 UDP 500/4500 및 ESP(IP 프로토콜 50)를 허용해야 합니다.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 기본 및 보조 터널 인터페이스 만들기

Palo Alto 방화벽에 로그인하고 **네트워크** > **인터페이스** > **터널** > **추가** 를 선택하고 다음 필드를 구성하고 **확인** 을 클릭합니다.

* 인터페이스 이름. 첫 번째 필드에 키워드 'tunnel'이 자동으로 입력됩니다. 인접 필드에 1에서 9999 사이의 숫자를 입력합니다. 이 인터페이스는 기본 터널 인터페이스로 사용되어 온-프레미스 데이터 센터와 프라이빗 클라우드 사이에서 사이트 간 트래픽을 전달합니다.
* 설명. 터널의 목적을 쉽게 식별할 수 있도록 설명을 입력합니다.
* Netflow 프로필. 기본값을 유지합니다.
* 구성. 인터페이스 할당 대상: 가상 라우터: **기본값** 을 선택합니다. 
        보안 영역: 신뢰할 수 있는 LAN 트래픽에 대한 영역을 선택합니다. 이 예에서 LAN 트래픽에 대한 영역의 이름은 'Trust'입니다.
* I p v 4입니다. **추가** 를 클릭하고 환경에서 겹치지 않는 미사용 /32 ip 주소를 추가합니다. 이 주소는 기본 터널 인터페이스에 할당되고 터널 모니터링에 사용됩니다(나중에 설명됨).

이 구성은 고가용성 VPN용이므로 2개의 터널 인터페이스(주 터널 인터페이스 1개와 보조 터널 인터페이스 1개)가 필요합니다. 이전 단계를 반복하여 보조 터널 인터페이스를 만듭니다. 다른 터널 ID와 다른 미사용 /32 ip 주소를 선택합니다.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 사이트 간 VPN을 통해 연결할 프라이빗 클라우드 서브넷의 고정 경로를 설정합니다.

온-프레미스 서브넷이 CloudSimple 프라이빗 클라우드 서브넷에 도달하려면 경로가 필요합니다.

**네트워크** > **가상 라우터** > *기본값* > **고정 경로** > **추가** 를 선택하고, 다음 필드를 구성하고, **확인** 을 클릭합니다.

* 이름. 경로의 용도를 쉽게 확인할 수 있도록 이름을 입력합니다.
* 대상. 온-프레미스에서 S2S 터널 인터페이스를 통해 연결할 CloudSimple 프라이빗 클라우드 서브넷을 지정합니다.
* 표시합니다. 드롭다운에서 1단계(섹션 2)에서 만든 주 터널 인터페이스를 선택합니다. 이 예에서는 tunnel.20입니다.
* 다음 홉입니다. **없음** 을 선택합니다.
* 관리자 거리입니다. 기본값을 유지합니다.
* 메트릭입니다. 1에서 65,535 사이의 값을 입력합니다. 핵심은 기본 터널 인터페이스에 해당하는 경로에 대해 보조 터널 인터페이스에 해당하는 경로보다 더 낮은 메트릭을 입력하여 기본 터널 인터페이스에 해당하는 경로가 선호되게 하는 것입니다. tunnel.20의 메트릭 값이 20이고 tunnel.30의 메트릭 값이 30이면 tunnel.20이 선호됩니다.
* 경로 테이블. 기본값을 유지합니다.
* BFD 프로필. 기본값을 유지합니다.
* 모니터링 중인 경로. 확인되지 않은 상태로 둡니다.

이전 단계를 반복하여 프라이빗 클라우드 서브넷이 보조 터널 인터페이스를 통해 보조/백업 경로로 사용할 다른 경로를 만듭니다. 이번에는 다른 터널 ID와 기본 경로보다 높은 메트릭을 선택합니다.

### <a name="3-define-the-cryptographic-profile"></a>3. 암호화 프로필 정의

IKEv1 1단계에서 VPN 터널을 설정하는 데 사용할 식별, 인증 및 암호화에 대한 프로토콜과 알고리즘을 지정하는 암호화 프로필을 정의합니다.

**네트워크** > **Expand Network Profiles(네트워크 프로필 확장)**  > **IKE Crypto(IKE 암호화)**  > **추가** 를 선택하고 다음 필드를 구성한 후 **확인** 을 클릭합니다.

* 이름. IKE 암호화 프로필의 이름을 입력합니다.
* DH 그룹. **추가** 를 클릭하고 적절한 DH 그룹을 선택합니다.
* 암호화 **추가** 를 클릭하고 적절한 암호화 방법을 선택합니다.
* 인증 **추가** 를 클릭하고 적절한 인증 방법을 선택합니다.
* 키 수명. 기본값을 유지합니다.
* IKEv2 인증 다중. 기본값을 유지합니다.

### <a name="4-define-ike-gateways"></a>4. IKE 게이트웨이 정의

VPN 터널의 각 끝에서 피어 간 통신을 설정하는 IKE 게이트웨이를 정의합니다.

**네트워크** > **Expand Network Profiles(네트워크 프로필 확장)**  > **IKE Gateways(IKE 게이트웨이)**  > **추가** 를 선택하고 다음 필드를 구성한 후 **확인** 을 클릭합니다.

일반 탭:

* 이름. 기본 CloudSimple VPN 피어와 피어링될 IKE 게이트웨이의 이름을 입력합니다.
* 버전. **IKEv1 only mode(IKEv1 전용 모드)** 를 선택합니다.
* 주소 형식. **IPv4** 를 선택합니다.
* 표시합니다. 공용 연결 또는 외부 인터페이스를 선택합니다.
* 로컬 IP 주소. 기본값을 유지합니다.
* 피어 IP 주소 형식. **IP** 를 선택합니다.
* 피어 주소. 기본 CloudSimple VPN 피어 IP 주소를 입력합니다.
* 인증 **미리 공유한 키** 를 선택합니다.
* 미리 공유한 키/미리 공유한 키 확인. CloudSimple VPN 게이트웨이 키와 일치하는 미리 공유한 키를 입력합니다.
* 로컬 ID. 온-프레미스 Palo Alto 방화벽의 공용 IP 주소를 입력합니다.
* 피어 ID. 기본 CloudSimple VPN 피어 IP 주소를 입력합니다.

고급 옵션 탭:

* 수동 모드 사용. 확인되지 않은 상태로 둡니다.
* NAT 통과를 사용합니다. 온-프레미스 Palo Alto 방화벽이 NAT 디바이스 뒤에 있지 않은 경우 선택하지 않은 상태로 둡니다. 그렇지 않으면 확인란을 선택합니다.

IKEv1:

* 교환 모드입니다. **기본** 을 선택합니다.
* IKE 암호화 프로필. 이전에 만든 IKE 암호화 프로필을 선택합니다. Enable Fragmentation(조각화 사용) 확인란을 선택하지 않은 상태로 둡니다.
* 작동하지 않는 피어 검색. 확인란을 선택하지 않은 상태로 둡니다.

이전 단계를 반복하여 보조 IKE 게이트웨이를 만듭니다.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IPSEC 암호화 프로필 정의

**네트워크** > **Expand Network Profiles(네트워크 프로필 확장)**  > **IPSEC Crypto(IPSEC 암호화)**  > **추가** 를 선택하고 다음 필드를 구성한 후 **확인** 을 클릭합니다.

* 이름. IPsec 암호화 프로필의 이름을 입력합니다.
* IPsec 프로토콜. **ESP** 를 선택합니다.
* 암호화 **추가** 를 클릭하고 적절한 암호화 방법을 선택합니다.
* 인증 **추가** 를 클릭하고 적절한 인증 방법을 선택합니다.
* DH 그룹. **no-pfs** 를 선택합니다.
* 수명. 30분으로 설정합니다.
* 사용하도록 설정합니다. 확인란을 선택하지 않은 상태로 둡니다.

이전 단계를 반복하여 보조 CloudSimple VPN 피어로 사용될 다른 IPsec 암호화 프로필을 만듭니다. 기본 및 보조 IPsec 터널 모두에 동일한 IPSEC 암호화 프로필을 사용할 수도 있습니다(다음 절차 참조).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 터널 모니터링에 대한 모니터 프로필 정의

**네트워크** > **Expand Network Profiles(네트워크 프로필 확장)**  > **모니터** > **추가** 를 선택하고 다음 필드를 구성한 후 **확인** 을 클릭합니다.

* 이름. 실패에 대한 사전 대응을 위해 터널 모니터링에 사용할 모니터 프로필의 이름을 입력합니다.
* 액션. **장애 조치(failover)** 를 선택합니다.
* 간격. 값 **3** 을 입력합니다.
* 임계값. 값 **7** 을 입력합니다.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 기본 및 보조 IPsec 터널 설정

**네트워크** > **IPsec Tunnels(IPsec 터널)**  > **추가** 를 선택하고 다음 필드를 구성한 후 **확인** 을 클릭합니다.

일반 탭:

* 이름. 기본 CloudSimple VPN 피어와 피어링할 기본 IPSEC 터널의 이름을 입력합니다.
* 터널 인터페이스. 기본 터널 인터페이스를 선택합니다.
* 을 입력한 다음 기본값을 유지합니다.
* 주소 형식. **IPv4** 를 선택합니다.
* IKE 게이트웨이. 기본 IKE 게이트웨이를 선택합니다.
* IPsec 암호화 프로필. 기본 IPsec 프로필을 선택합니다. **고급 옵션 표시** 를 선택합니다.
* 재생 방지 사용. 기본값을 유지합니다.
* TOS 헤더 복사. 확인란을 선택하지 않은 상태로 둡니다.
* 터널 모니터. 확인란을 선택합니다.
* 대상 IP. 사이트 간 연결을 통해 허용되는 CloudSimple 프라이빗 클라우드 서브넷에 속하는 IP 주소를 입력합니다. Palo Alto의 터널 인터페이스(예: tunnel.20 - 10.64.5.2/32 및 tunnel.30 - 10.64.6.2/32)가 사이트 간 VPN을 통해 CloudSimple 프라이빗 클라우드 IP 주소에 도달할 수 있는지 확인합니다. 프록시 ID에 대한 다음 구성을 참조하세요.
* 프로필. 모니터 프로필을 선택합니다.

프록시 ID 탭: **IPv4** > **추가** 를 클릭하고 다음을 구성합니다.

* 프록시 ID. 관심 있는 트래픽에 대한 이름을 입력합니다. 하나의 IPsec 터널 내에서 여러 프록시 ID가 전달될 수 있습니다.
* 로컬. 사이트 간 VPN을 통해 프라이빗 클라우드 서브넷과 통신할 수 있는 온-프레미스 로컬 서브넷을 지정합니다.
* 원격. 로컬 서브넷과 통신할 수 있는 프라이빗 클라우드 원격 서브넷을 지정합니다.
* 프로토콜. **모두** 를 선택합니다.

이전 단계를 반복하여 보조 CloudSimple VPN 피어에 사용할 다른 IPsec 터널을 만듭니다.

## <a name="references"></a>참조

Cisco ASA에서 NAT 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 시리즈 구성 가이드</a>

Cisco ASA에서 지원되는 IKEv1 및 IKEv2 특성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa72/configuration/guide/conf_gd/ike.html" target="_blank">Cisco ASA 시리즈 CLI 구성 가이드</a>

버전 8.4 이상을 사용하여 Cisco ASA에서 IPsec 사이트 간 VPN 구성:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">ASA에서 ASDM 또는 CLI를 사용하여 IKEv1 IPsec 사이트 간 터널 구성</a>

Azure에서 Cisco ASAv(Adaptive Security Appliance Virtual) 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco ASAv(Adaptive Security Appliance Virtual) 빠른 시작 가이드</a>

Palo Alto에서 프록시 ID를 사용하여 사이트 간 VPN 구성:

[사이트 간 VPN 설정](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

터널 모니터 설정:

[터널 모니터링 설정](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 게이트웨이 또는 IPsec 터널 작업:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IKE 게이트웨이 또는 IPsec 터널 사용/사용 안 함, 새로 고침 또는 다시 시작</a>
