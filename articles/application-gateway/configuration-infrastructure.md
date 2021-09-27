---
title: Azure Application Gateway 인프라 구성
description: 이 문서에서는 Azure Application Gateway 인프라를 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: surmb
ms.openlocfilehash: 5ea384a580109d40dfbb072b7bad16924307873e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837331"
---
# <a name="application-gateway-infrastructure-configuration"></a>Application Gateway 인프라 구성

애플리케이션 게이트웨이 인프라에는 가상 네트워크, 서브넷, 네트워크 보안 그룹 및 사용자 정의 경로가 포함됩니다.

## <a name="virtual-network-and-dedicated-subnet"></a>가상 네트워크 및 전용 서브넷

애플리케이션 게이트웨이는 가상 네트워크에서 전용 배포입니다. 가상 네트워크 내에는 애플리케이션 게이트웨이에 대한 전용 서브넷이 필요합니다. 특정 애플리케이션 게이트웨이 배포의 인스턴스를 서브넷에 여러 개 포함할 수 있습니다. 또한 서브넷에 다른 애플리케이션 게이트웨이를 배포할 수도 있습니다. 그러나 애플리케이션 게이트웨이 서브넷에 다른 리소스를 배포할 수 없습니다. 동일한 서브넷에서 Standard_v2와 표준 Azure Application Gateway를 혼합할 수 없습니다.

> [!NOTE]
> [가상 네트워크 서비스 엔드포인트 정책](../virtual-network/virtual-network-service-endpoint-policies-overview.md)은 현재 Application Gateway 서브넷에서 지원되지 않습니다.

### <a name="size-of-the-subnet"></a>서브넷 크기

Application Gateway는 인스턴스당 하나의 개인 IP 주소를 사용하고 프라이빗 프런트 엔드 IP가 구성된 경우 또 다른 개인 IP 주소를 사용합니다.

Azure는 또한 내부용으로 각 서브넷에 처음 4개의 IP 주소와 마지막 IP 주소 등 5개의 IP 주소를 예약합니다. 예를 들어 프라이빗 프런트 엔드 IP가 없는 15개의 애플리케이션 게이트웨이 인스턴스를 고려합니다. 이 서브넷에는 내부용으로 5개, 애플리케이션 게이트웨이 인스턴스용으로 15개 등 20개 이상의 IP 주소가 필요합니다.

27개의 애플리케이션 게이트웨이 인스턴스와 프라이빗 프런트 엔드 IP에 대한 IP 주소가 있는 서브넷을 고려합니다. 이 경우 애플리케이션 게이트웨이 인스턴스용으로 27개, 프라이빗 프런트 엔드용으로 1개와 내부용으로 5개 등 33개의 IP 주소가 필요합니다.

Application Gateway(Standard 또는 WAF) SKU는 최대 32개의 인스턴스(32개의 인스턴스 IP 주소 + 1개의 프라이빗 프런트 엔드 IP + 5개의 Azure 예약됨)를 지원할 수 있으므로 최소 서브넷 크기인 /26을 권장합니다.

Application Gateway(Standard_v2 또는 WAF_v2 SKU)는 최대 125개의 인스턴스를 지원할 수 있습니다(125개의 인스턴스 IP 주소 + 1개의 프라이빗 프런트 엔드 IP + 5개의 Azure 예약됨). 최소 서브넷 크기 /24를 권장합니다.

> [!IMPORTANT]
> /24 서브넷은 Application Gateway v2 SKU 배포당 필요하지 않지만 매우 권장됩니다. 이는 Application Gateway v2에 확장 및 유지 관리 업그레이드의 자동 크기 조정에 충분한 공간이 있는지 확인하기 위한 것입니다. Application Gateway v2 서브넷에 최대 예상 트래픽을 처리하는 데 필요한 인스턴스 수를 수용할 수 있는 충분한 주소 공간이 있는지 확인해야 합니다. 최대 인스턴스 수를 지정하는 경우 서브넷에 최소한 많은 주소에 대한 용량이 있어야 합니다. 인스턴스 수에 대한 용량 계획은 [인스턴스 수 세부 정보](understanding-pricing.md#instance-count)를 참조하세요.

> [!TIP]
> 동일한 가상 네트워크 내에서 기존 Application Gateway의 서브넷을 변경할 수 있습니다. Azure PowerShell 또는 Azure CLI를 사용하여 이를 수행할 수 있습니다. 자세한 내용은 [Application Gateway에 대한 질문과 대답](application-gateway-faq.yml#can-i-change-the-virtual-network-or-subnet-for-an-existing-application-gateway)을 참조하세요.

## <a name="network-security-groups"></a>네트워크 보안 그룹

NSG(네트워크 보안 그룹)는 Application Gateway에서 지원됩니다. 그러나 몇 가지 제한 사항이 있습니다.

- 들어오는 인터넷 트래픽을 Application Gateway v1 SKU의 경우 65503-65534 TCP 포트에서 허용하고, 대상 서브넷이 **Any** 이고 원본이 **GatewayManager** 서비스 태그인 v2 SKU의 경우 65200-65535 TCP 포트에서 허용해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호(잠김)됩니다. 해당 게이트웨이의 고객을 포함한 외부 엔터티는 해당 엔드포인트에서 통신할 수 없습니다.

- 아웃바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃바운드 규칙은 인터넷 연결을 허용합니다. 다음을 수행하는 것이 좋습니다.

  - 기본 아웃바운드 규칙을 제거하지 마십시오.
  - 아웃바운드 연결을 거부하는 다른 아웃바운드 규칙은 만들지 마십시오.

- 대상 서브넷이 **Any** 인 **AzureLoadBalancer** 태그의 트래픽을 허용해야 합니다.

### <a name="allow-access-to-a-few-source-ips"></a>몇 가지 원본 IP에 대한 액세스 허용

이 시나리오에서는 Application Gateway 서브넷에 NSG를 사용합니다. 이 우선 순위에 따라 서브넷에 다음과 같은 제한 사항을 적용합니다.

1. 인바운드 액세스 포트(예: HTTP 액세스용 포트 80)로 전체 Application Gateway 서브넷 주소 범위 및 대상 포트를 대상으로 하는 원본 IP 또는 IP 범위에서 들어오는 트래픽을 허용합니다.
2. 원본에서 들어오는 요청을 **GatewayManager** 서비스 태그로 허용하고 Application Gateway v1 SKU의 경우 대상을 **Any** 로 대상 포트를 65503-65534로 허용하고, [ 백엔드 상태 통신](./application-gateway-diagnostics.md)의 경우 v2 SKU의 경우 포트 65200-65535를 허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호(잠김)됩니다. 적절한 인증서가 없는 경우 외부 엔터티는 해당 엔드포인트에 대한 변경 내용을 초기화할 수 없습니다.
3. [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)에 대한 들어오는 Azure Load Balancer 프로브(*AzureLoadBalancer* 태그) 및 인바운드 가상 네트워크 트래픽(*VirtualNetwork* 태그)을 허용합니다.
4. 모두 거부 규칙을 사용하여 다른 모든 들어오는 트래픽을 차단합니다.
5. 모든 대상에 대해 인터넷으로의 아웃바운드 트래픽을 허용합니다.

## <a name="supported-user-defined-routes"></a>지원되는 사용자 정의 경로 

> [!IMPORTANT]
> Application Gateway 서브넷에서 UDR을 사용하면 [백 엔드 상태 보기](./application-gateway-diagnostics.md#back-end-health)의 상태가 **알 수 없음** 으로 표시될 수 있습니다. 이로 인해 Application Gateway 로그 및 메트릭이 생성되지 않을 수도 있습니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있도록 Application Gateway 서브넷에서 UDR을 사용하지 않는 것이 좋습니다.

- **v1**

   v1 SKU의 경우 UDR(사용자 정의 경로)은 엔드투엔드 요청/응답 통신을 변경하지 않는 한 Application Gateway 서브넷에서 지원됩니다. 예를 들어 Application Gateway 서브넷에서 UDR을 설정하여 패킷 검사를 위한 방화벽 어플라이언스를 가리키도록 할 수 있습니다. 그러나 검사 후 패킷이 의도한 대상에 도달할 수 있는지 확인해야 합니다. 이렇게 하지 않으면 잘못된 상태 프로브 또는 트래픽 라우팅 동작을 초래할 수 있습니다. 여기에는 가상 네트워크에서 Azure ExpressRoute 또는 VPN 게이트웨이를 통해 전파된 학습한 경로 또는 기본 0.0.0.0/0 경로가 포함됩니다. 0\.0.0.0/0을 온-프레미스(강제 터널링)에서 리디렉션해야 하는 모든 시나리오는 v1에서 지원되지 않습니다.

- **v2**

   v2 SKU의 경우 지원되는 시나리오와 지원되지 않는 시나리오는 다음과 같습니다.

   **v2 지원되는 시나리오**
   > [!WARNING]
   > 경로 테이블을 잘못 구성하면 Application Gateway v2에서 비대칭 라우팅이 발생할 수 있습니다. 모든 관리/컨트롤 플레인 트래픽이 가상 어플라이언스를 통하지 않고 인터넷으로 직접 전송되는지 확인합니다. 로깅 및 메트릭도 영향을 받을 수 있습니다.


  **시나리오 1**: Application Gateway 서브넷에 대한 BGP(Border Gateway Protocol) 경로 전파를 사용하지 않도록 설정하는 UDR

   경우에 따라 기본 게이트웨이 경로(0.0.0.0/0)가 Application Gateway 가상 네트워크와 연결된 ExpressRoute 또는 VPN 게이트웨이를 통해 보급됩니다. 이렇게 하면 인터넷에 직접 경로를 사용해야 하는 관리 평면 트래픽이 중단됩니다. 이러한 시나리오에서는 UDR을 사용하여 BGP 경로 전파를 사용하지 않도록 설정할 수 있습니다. 

   BGP 경로 전파를 사용하지 않도록 설정하려면 다음 단계를 사용합니다.

   1. Azure에서 경로 테이블 리소스를 만듭니다.
   2. **가상 네트워크 게이트웨이 경로 전파** 매개 변수를 사용하지 않도록 설정합니다. 
   3. 경로 테이블을 적절한 서브넷에 연결합니다. 

   이 시나리오에 UDR을 사용하도록 설정하면 기존 설정이 중단되지 않습니다.

  **시나리오 2**: 인터넷에 0.0.0.0/0을 전달하는 UDR

   UDR을 만들어 0.0.0.0/0 트래픽을 인터넷으로 직접 보낼 수 있습니다. 

  **시나리오 3**: kubenet을 사용하는 Azure Kubernetes Service에 대한 UDR

  AKS(Azure Kubernetes Service) 및 AGIC(Application Gateway 수신 컨트롤러)와 함께 kubenet을 사용하는 경우 Application Gateway에서 Pod로 전송된 트래픽을 올바른 노드로 라우팅할 수 있도록 경로 테이블이 필요합니다. Azure CNI를 사용하는 경우에는 이 작업이 필요하지 않습니다. 

  kubenet이 작동할 수 있도록 경로 테이블을 사용하려면 다음 단계를 수행합니다.

  1. AKS에서 만든 리소스 그룹으로 이동합니다(리소스 그룹의 이름은 "MC_"로 시작해야 함).
  2. 해당 리소스 그룹에서 AKS에 의해 생성된 경로 테이블을 찾습니다. 경로 테이블은 다음 정보로 채워야 합니다.
     - 주소 접두사는 AKS에서 연결하려는 Pod의 IP 범위여야 합니다. 
     - 다음 홉 형식은 가상 어플라이언스여야 합니다. 
     - 다음 홉 주소는 Pod를 호스트하는 노드의 IP 주소여야 합니다.
  3. 이 경로 테이블을 Application Gateway 서브넷에 연결합니다. 
    
  **v2 지원되지 않는 시나리오**

  **시나리오 1**: 가상 어플라이언스에 대한 UDR

  가상 어플라이언스, 허브/스포크 가상 네트워크 또는 온-프레미스(강제 터널링)를 통해 0.0.0.0/0을 리디렉션해야 하는 시나리오는 V2에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [프런트 엔드 IP 주소 구성에 대해 알아봅니다](configuration-front-end-ip.md).
