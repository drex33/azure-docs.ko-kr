---
title: 자습서 - 네트워크 계획 검사 목록
description: Azure VMware Solution의 네트워크 연결 및 네트워크 포트에 대한 네트워크 요구 사항에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 07/01/2021
ms.openlocfilehash: cee4768cbf6382b3cce229267f9b2483c353b464
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314498"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 네트워킹 계획 검사 목록 

Azure VMware 솔루션은 온-프레미스 및 Azure 기반 환경 또는 리소스의 사용자와 애플리케이션에 액세스할 수 있는 VMware 프라이빗 클라우드 환경을 제공합니다. Azure ExpressRoute 및 VPN 연결과 같은 네트워킹 서비스를 통해 연결됩니다. 서비스를 사용하려면 특정 네트워크 주소 범위와 방화벽 포트가 필요합니다. 이 문서에서는 Azure VMware Solution에서 작동하도록 네트워킹을 올바르게 구성하는 데 필요한 정보를 제공합니다.

이 자습서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 가상 네트워크 및 ExpressRoute 회로 고려 사항
> * 라우팅 및 서브넷 요구 사항
> * 서비스와 통신하는 데 필요한 네트워크 포트
> * Azure VMware Solution의 DHCP 및 DNS 고려 사항

## <a name="prerequisite"></a>필수 조건
ExpressRoute 공급자 서비스를 포함한 모든 게이트웨이가 4바이트 ASN(자율 시스템 번호)을 지원하는지 확인합니다. Azure VMware Solution은 경고를 알리는 데 4바이트 공용 ASN을 사용합니다.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>가상 네트워크 및 ExpressRoute 회로 고려 사항
구독에서 가상 네트워크 연결을 만들 때 ExpressRoute 회로는 권한 부여 키, 그리고 Azure Portal에서 요청하는 피어링 ID를 사용하여 피어링을 통해 설정됩니다. 피어링은 프라이빗 클라우드와 가상 네트워크 간의 일대일 연결입니다.

> [!NOTE] 
> ExpressRoute 회로는 프라이빗 클라우드 배포에 포함되지 않습니다. 온-프레미스 ExpressRoute 회로는 이 문서의 범위를 벗어났습니다. 프라이빗 클라우드에 대한 온-프레미스 연결이 필요한 경우 기존 ExpressRoute 회로 중 하나를 사용하거나 Azure Portal에서 구매할 수 있습니다.

프라이빗 클라우드를 배포할 때 vCenter 및 NSX-T Manager에 대한 IP 주소를 수신합니다. 이러한 관리 인터페이스에 액세스하려면 구독의 가상 네트워크에 더 많은 리소스를 만들어야 합니다. 자습서에서 이러한 리소스를 만들고 [ExpressRoute 프라이빗 피어링](tutorial-expressroute-global-reach-private-cloud.md)을 설정하는 절차를 찾을 수 있습니다.

프라이빗 클라우드 논리 네트워킹에는 미리 프로비저닝된 NSX-T가 제공됩니다. 계층-0 게이트웨이 및 계층-1 게이트웨이는 사용자를 위해 미리 프로비저닝됩니다. 세그먼트를 만들고 기존 계층-1 게이트웨이에 연결하거나 정의한 새 계층-1 게이트웨이에 연결할 수 있습니다. NSX-T 논리 네트워킹 구성 요소는 워크로드 간에 동-서 연결을 제공하고 인터넷 및 Azure 서비스에 대한 북-남 연결을 제공합니다.

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

## <a name="routing-and-subnet-considerations"></a>라우팅 및 서브넷 고려 사항
Azure VMware Solution 프라이빗 클라우드는 Azure ExpressRoute 연결을 사용하여 Azure 가상 네트워크에 연결됩니다. 높은 대역폭이 높고 대기 시간이 짧은 이 연결을 사용하면 프라이빗 클라우드 환경의 Azure 구독에서 실행되는 서비스에 액세스할 수 있습니다. 라우팅은 BGP(Border Gateway Protocol) 기반이며, 자동으로 프로비저닝되며, 각 프라이빗 클라우드 배포에 대해 기본적으로 사용하도록 설정됩니다. 

Azure VMware Solution 프라이빗 클라우드는 최소한 `/22` CIDR 네트워크 주소 블록 이상이 필요합니다(아래 참조). 이 네트워크는 온-프레미스 네트워크를 보완합니다. 그러므로 주소 블록은 구독과 온-프레미스 네트워크의 다른 가상 네트워크에서 사용되는 주소 블록과 겹치지 않아야 합니다. 이 주소 블록 내에서 관리, 프로비저닝 및 vMotion 네트워크는 자동으로 프로비저닝됩니다.

>[!NOTE]
>주소 블록에 허용되는 범위는 RFC 1918 개인 주소 공간(10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)입니다(172.17.0.0/16 제외).

`/22` CIDR 네트워크 주소 블록의 예: `10.10.0.0/22`

서브넷:

| 네트워크 사용량             | 서브넷 | 예제          |
| ------------------------- | ------ | ---------------- |
| 프라이빗 클라우드 관리  | `/26`  | `10.10.0.0/26`   |
| HCX Mgmt 마이그레이션       | `/26`  | `10.10.0.64/26`  |
| Global Reach 예약됨     | `/26`  | `10.10.0.128/26` |
| NSX-T DNS 서비스         | `/32`  | `10.10.0.192/32` |
| 예약됨                  | `/32`  | `10.10.0.193/32` |
| 예약됨                  | `/32`  | `10.10.0.194/32` |
| 예약됨                  | `/32`  | `10.10.0.195/32` |
| 예약됨                  | `/30`  | `10.10.0.196/30` |
| 예약됨                  | `/29`  | `10.10.0.200/29` |
| 예약됨                  | `/28`  | `10.10.0.208/28` |
| ExpressRoute 피어링      | `/27`  | `10.10.0.224/27` |
| ESXi 관리           | `/25`  | `10.10.1.0/25`   |
| vMotion 네트워크           | `/25`  | `10.10.1.128/25` |
| 복제 네트워크       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX 업링크                | `/26`  | `10.10.3.0/26`   |
| 예약됨                  | `/26`  | `10.10.3.64/26`  |
| 예약됨                  | `/26`  | `10.10.3.128/26` |
| 예약됨                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>필수 네트워크 포트

| 원본 | 대상 | 프로토콜 | 포트 | Description  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| 프라이빗 클라우드 DNS 서버 | 온-프레미스 DNS 서버 | UDP | 53 | DNS 클라이언트 - 온-프레미스 DNS 쿼리에 대한 PC vCenter의 요청 전달(아래 DNS 섹션 확인) |  
| 온-프레미스 DNS 서버   | 프라이빗 클라우드 DNS 서버 | UDP | 53 | DNS 클라이언트 - 온-프레미스 서비스의 요청을 프라이빗 클라우드 DNS 서버로 전달(아래 DNS 섹션 확인) |  
| 온-프레미스 네트워크  | 프라이빗 클라우드 vCenter 서버  | TCP(HTTP)  | 80 | vCenter Server에는 직접 HTTP 연결을 위해 80 포트가 필요합니다. 포트 80은 요청을 HTTPS 포트 443으로 리디렉션합니다. 이 리디렉션은 `https://server` 대신 `http://server`를 사용하는 경우 유용합니다.  |  
| 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory  | TCP  | 389/636 | 이러한 포트는 Azure VMware Solutions vCenter가 온-프레미스 Active Directory/LDAP 서버와 통신할 수 있도록 열려 있습니다.  이러한 포트는 선택 사항이며, 프라이빗 클라우드 vCenter에서 온-프레미스 AD를 ID 원본으로 구성하기 위한 것입니다. 보안을 위해 포트 636을 사용하는 것이 좋습니다. |  
| 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory 글로벌 카탈로그  | TCP  | 3268/3269 | 이러한 포트는 Azure VMware Solutions vCenter가 온-프레미스 Active Directory/LDAP 글로벌 카탈로그 서버와 통신할 수 있도록 열려 있습니다.  이러한 포트는 선택 사항이며, 프라이빗 클라우드 vCenter에서 온-프레미스 AD를 ID 원본으로 구성하기 위한 것입니다. 보안을 위해 포트 3269를 사용하는 것이 좋습니다. |  
| 온-프레미스 네트워크  | 프라이빗 클라우드 vCenter 서버  | TCP(HTTPS)  | 443 | 이 포트를 사용하면 온-프레미스 네트워크에서 vCenter에 액세스할 수 있습니다. vCenter Server 시스템에서 vSphere Client의 연결을 수신 대기하는 데 사용하는 기본 포트입니다. vCenter Server 시스템에서 vSphere Client의 데이터를 받을 수 있도록 하려면 방화벽에서 포트 443을 엽니다. 또한 vCenter Server 시스템은 포트 443을 사용하여 SDK 클라이언트로부터의 데이터 전송을 모니터링합니다. |  
| 온-프레미스 네트워크  | HCX 관리자  | TCP(HTTPS) | 9443 | 하이브리드 클라우드 관리자 시스템 구성을 위한 하이브리드 클라우드 관리자 가상 어플라이언스 관리 인터페이스입니다. |
| 관리자 네트워크  | 하이브리드 클라우드 관리자 | SSH | 22 | 하이브리드 클라우드 관리자에 대한 관리자 SSH 액세스입니다. |
| HCX 관리자 | 클라우드 게이트웨이 | TCP(HTTPS) | 8123 | 호스트 기반 복제 서비스 지침을 하이브리드 클라우드 게이트웨이로 보냅니다. |
| HCX 관리자 | 클라우드 게이트웨이 | HTTP TCP(HTTPS) | 9443 | REST API를 사용하여 관리 지침을 로컬 하이브리드 클라우드 게이트웨이로 보냅니다. |
| 클라우드 게이트웨이 | L2C | TCP(HTTPS) | 443 | L2C에서 하이브리드 클라우드 게이트웨이와 동일한 경로를 사용하는 경우 관리 지침을 클라우드 게이트웨이에서 L2C로 보냅니다. |
| 클라우드 게이트웨이 | ESXi 호스트 | TCP | 80,902 | 관리 및 OVF 배포 |
| 클라우드 게이트웨이(로컬)| 클라우드 게이트웨이(원격) | UDP | 4500 | IPSEC Internet Key Exchange(IKEv2)가<br>   양방향 터널에 대한 워크로드를 캡슐화하는 데 필요합니다. NAT-T(Network Address Translation-Traversal)도 지원됩니다. |
| 클라우드 게이트웨이(로컬) | 클라우드 게이트웨이(원격)  | UDP | 500 | 양방향 터널에 대한<br> IPSEC Internet Key Exchange(ISAKMP)에 필요합니다. |
| 온-프레미스 vCenter 네트워크 | 프라이빗 클라우드 관리 네트워크 | TCP | 8000 |  온-프레미스 vCenter에서 프라이빗 클라우드 vCenter로 VM의 vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP 및 DNS 확인 고려 사항

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VMware Solution 프라이빗 클라우드를 배포하기 위한 고려 사항 및 요구 사항에 대해 알아보았습니다. 적절한 네트워킹이 제대로 준비되면 Azure VMware Solution 프라이빗 클라우드를 만들기 위해 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure VMware Solution 프라이빗 클라우드 만들기](tutorial-create-private-cloud.md)
