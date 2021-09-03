---
title: Azure VMware Solution 워크로드에 대해 공용 인터넷 사용
description: 이 문서에서는 Azure Virtual WAN에서 공용 IP 기능을 사용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/25/2021
ms.openlocfilehash: bae760da5da39118f32b5d0b4dfa661a81727f3c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567907"
---
# <a name="enable-public-internet-for-azure-vmware-solution-workloads"></a>Azure VMware Solution 워크로드에 대해 공용 인터넷 사용

공용 IP는 Azure VMware Solution 연결의 기능입니다. 웹 서버, VM(가상 머신) 및 공용 네트워크를 통해 액세스할 수 있는 호스트와 같은 리소스를 만듭니다. 

두 가지 방법으로 퍼블릭 인터넷 액세스를 사용할 수 있습니다. 

- 애플리케이션을 HTTP/HTTPS 트래픽에 대한 Application Gateway 부하 분산 장치에서 호스트하고 게시합니다.

- Azure Virtual WAN의 공용 IP 기능을 통해 게시합니다.

Azure VMware Solution 프라이빗 클라우드 배포의 일부로 공용 IP 기능을 사용하면 자동화된 필수 구성 요소가 만들어지고 사용할 수 있게 됩니다.

-  가상 WAN

-  ExpressRoute 연결을 사용하는 Virtual WAN 허브

-  공용 IP를 사용하는 Azure Firewall 서비스

이 문서에서는 Virtual WAN에서 공용 IP 기능을 사용하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure VMware Solution 환경

- Azure VMware Solution 환경에서 실행 중인 웹 서버입니다.

- Virtual WAN 허브 배포에 쓰이는 새로운 겹치지 않는 IP 범위(일반적으로 `/24`)입니다.

## <a name="reference-architecture"></a>참조 아키텍처

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Azure VMware Solution의 공용 IP 아키텍처를 보여 주는 다이어그램." border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

아키텍처 다이어그램은 Azure VMware Solution 환경에서 호스트되고 RFC1918 개인 IP 주소를 사용하여 구성된 웹 서버를 보여 줍니다.  웹 서비스는 Virtual WAN 공용 IP 기능을 통해 인터넷에서 활성화됩니다.  공용 IP는 일반적으로 Azure Firewall에서 변환되는 대상 NAT입니다. DNAT 규칙을 사용하는 방화벽 정책은 공용 IP 주소 요청을 포트를 사용하는 프라이빗 주소(웹 서버)로 변환합니다.

사용자 요청은 공용 IP로 방화벽에 도달하고, 그 다음 Azure Firewall에서 DNAT 규칙을 사용하는 개인 IP로 변환됩니다. 방화벽은 NAT 테이블을 검사하고, 요청이 항목과 일치하는 경우에 트래픽을 변환된 주소로 착신 전환하고 Azure VMware Solution 환경으로 이동합니다.

웹 서버는 요청을 수신하고 요청된 정보 또는 페이지를 사용하여 방화벽에 회신한 다음 사용자에게 공용 IP 주소에 대한 정보를 전달합니다.

## <a name="test-case"></a>테스트 사례
이 시나리오에서는 IIS 웹 서버를 인터넷에 게시합니다. Azure VMware Solution의 공용 IP 기능을 사용하여 공용 IP 주소에 웹 사이트를 게시합니다.  또한 방화벽에서 NAT 규칙을 구성하고 공용 IP를 사용하는 Azure VMware Solution 리소스(웹 서버를 사용하는 VM)에 액세스합니다.

>[!TIP]
>송신 트래픽을 사용하려면 보안 구성 > **Azure Firewall** 에 대한 인터넷 트래픽을 설정해야 합니다.

## <a name="deploy-virtual-wan"></a>Virtual WAN을 배포합니다.

1. Azure Portal에 로그인하여 **Azure VMware Solution** 을 검색하고 선택합니다.

1. Azure VMware Solution 프라이빗 클라우드를 선택합니다.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware Solution 프라이빗 클라우드의 스크린샷." lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. **관리** 에서 **연결** 을 선택합니다.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="연결 섹션의 스크린샷." lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. **공용 IP** 탭을 선택한 다음 **구성** 을 선택합니다.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="공용 IP 구성 시작 위치를 보여 주는 스크린샷" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 기본값을 그대로 적용하거나 변경하고 **만들기** 를 선택합니다.

   - Virtual WAN 리소스 그룹

   - Virtual WAN 이름

   - 가상 허브 주소 블록(겹치지 않는 새 IP 범위 사용)

   - 공용 IP 수(1-100)

모든 구성 요소의 배포를 완료하는 데 약 1시간이 걸립니다. 이 배포는 한 번만 수행하며 추후에 이 Azure VMware Solution 환경에 쓰일 공용 IP를 지원합니다.  

>[!TIP]
>**알림** 영역에서 상태를 모니터링할 수 있습니다. 

## <a name="view-and-add-public-ip-addresses"></a>공용 IP 주소 보기 및 추가

아래 단계를 수행하여 더 많은 공용 IP 주소를 확인하고 추가할 수 있습니다.

1. Azure Portal에서 **방화벽** 을 검색하여 선택합니다.

1. 배포된 방화벽을 선택하고 **Azure Firewall Manager를 방문하여 이 방화벽을 구성하고 관리** 를 선택합니다.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="방화벽을 구성하고 관리하는 옵션을 보여 주는 스크린샷" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **보안 가상 허브** 를 선택하고, 목록에서 가상 허브를 선택합니다.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Firewall Manager의 스크린샷" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 가상 허브 페이지에서 **공용 IP 구성** 을 선택하고, 공용 IP 주소를 더 추가하려면 **추가** 를 선택합니다. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Firewall Manager에서 공용 IP 구성을 추가하는 방법의 스크린샷" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 필요한 IP 수를 제공하고 **추가** 를 선택합니다.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="지정된 수의 공용 IP 구성을 추가하는 스크린샷":::


## <a name="create-firewall-policies"></a>방화벽 정책 만들기

모든 구성 요소를 배포한 후에는 추가된 리소스 그룹에서 구성 요소를 볼 수 있습니다. 다음 단계는 방화벽 정책을 추가하는 것입니다.

1. Azure Portal에서 **방화벽** 을 검색하여 선택합니다.

1. 배포된 방화벽을 선택하고 **Azure Firewall Manager를 방문하여 이 방화벽을 구성하고 관리** 를 선택합니다.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="방화벽을 구성하고 관리하는 옵션을 보여 주는 스크린샷" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **Azure Firewall 정책** 을 선택하고 **Azure Firewall 정책 만들기** 를 선택합니다.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Firewall Manager에서 방화벽 정책을 만드는 방법의 스크린샷" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. **기본 사항** 탭에서 필요한 세부 정보를 입력하고 **다음: DNS 설정** 을 선택합니다. 

1. **DNS** 탭에서 **사용 안 함** 을 선택하고 **다음: 규칙** 을 선택합니다.

1. **규칙 컬렉션 추가** 를 선택하고 아래 세부 정보를 제공한 다음 **추가** 를 선택합니다. 그런 후 **다음: 위협 인텔리전스** 를 선택합니다.

   -  Name
   -  규칙 컬렉션 유형 - **DNAT**
   -  우선 순위
   -  규칙 컬렉션 작업 – **허용**
   -  규칙 이름
   -  원본 유형- **IPaddress**
   -  원본 - **\***
   -  프로토콜 - **TCP**
   -  대상 포트 - **80**
   -  대상 유형 - **IP 주소**
   -  대상 - **공용 IP 주소**
   -  변환된 주소 – **Azure VMware Solution 웹 서버 개인 IP 주소**
   -  변환된 포트 - **Azure VMware Solution 웹 서버 포트**

1. 기본값에서 나간 후 **다음: 허브** 를 선택합니다.

1. **가상 허브 연결** 을 선택합니다.

1. 목록에서 허브를 선택한 다음, **추가** 를 선택합니다.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="보안 가상 허브로 변환될 선택된 허브를 보여 주는 스크린샷." lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. 완료되면 **다음: 태그** 를 선택합니다. 

1. (선택 사항)사용자 리소스를 범주화하는 이름 및 값 쌍을 만듭니다. 

1. **다음: 검토 및 만들기** 를 선택하고 **만들기** 를 선택합니다.

## <a name="limitations"></a>제한 사항

프라이빗 클라우드 당 100개의 공용 IP가 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution에서 공용 IP 기능을 사용하는 방법을 살펴 보았으므로 다음에 대해 알아볼 수 있습니다.

- [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md)에 공용 IP 주소 사용.
- [Azure VMware Solution에 IPSec 터널 생성](./configure-site-to-site-vpn-gateway.md).
