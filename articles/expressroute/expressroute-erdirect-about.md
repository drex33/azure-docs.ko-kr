---
title: Azure ExpressRoute Direct 정보
description: 'Azure ExpressRoute Direct의 주요 기능 및 ExpressRoute Direct에 온보딩하는 데 필요한 정보(예: 사용 가능한 SKU, 기술 요구 사항)에 대해 알아봅니다.'
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: duau
ms.openlocfilehash: 01de2c75cbb6c1f8b2e153e18add612608e75ac7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433984"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct 정보

ExpressRoute Direct는 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. ExpressRoute Direct는 대규모로 활성/활성 연결을 지원하는 이중 100Gbps 또는 10Gbps 연결을 제공합니다. ER Direct용 서비스 공급자를 사용할 수 있습니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* 스토리지 및 Cosmos DB와 같은 서비스로 대규모 데이터 수집
* 은행, 정부, 소매업체 등 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

## <a name="onboard-to-expressroute-direct"></a>ExpressRoute Direct에 온보딩

ExpressRoute Direct를 사용하려면 먼저 구독을 등록해야 합니다. 등록하려면 Azure PowerShell을 사용하여 다음 명령을 실행합니다.

1.  Azure에 로그인하고 등록할 구독을 선택합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 다음 명령을 사용하여 퍼블릭 미리 보기에 대한 구독을 등록합니다.

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

등록한 후에는 **Microsoft.Network** 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다.

1. [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)에 설명된 대로 구독 설정에 액세스합니다.

1. 구독에서 **리소스 공급자** 에 대해 **Microsoft.Network** 공급자가 **등록됨** 상태로 표시되는지 확인합니다. Microsoft.Network 리소스 공급자가 등록된 공급자 목록에 없는 경우 추가합니다.

ExpressRoute Direct 사용을 시작했는데 선택한 피어링 위치에 사용 가능한 포트가 없는 경우, 지원 요청을 로그하여 인벤토리를 추가로 요청합니다.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>서비스 공급자 및 ExpressRoute Direct를 사용하는 ExpressRoute

| **서비스 공급자를 사용하는 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 서비스 공급자를 활용하여 기존 인프라로 빠른 온보딩 및 연결 활성화 | 100Gbps/10Gbps 인프라 및 모든 계층의 전체 관리 필요
| 이더넷 및 MPLS를 포함하는 수백 개의 공급자와 통합 | 규제 산업 및 대규모 데이터 수집에 대한 직접/전용 용량 |
| 50Mbps - 10Gbps의 회로 SKU | 100Gbps ExpressRoute Direct의 경우 고객은 다음 회로 SKU 조합 중 선택할 수 있습니다. <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> 10Gbps ExpressRoute Direct의 경우 고객은 다음 회로 SKU 조합 중 선택할 수 있습니다.<ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>
| 단일 테넌트에 최적화됨 | 여러 사업부 및 여러 작업 환경에서 단일 테넌트에 대해 최적화됨

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 회로

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure 및 Microsoft 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

각 피어링 위치는 Microsoft의 글로벌 네트워크에 대한 액세스 권한을 가지며 기본적으로 지리적 영역에 있는 모든 지역에 액세스할 수 있으며 프리미엄 회로를 사용하여 전 세계 모든 지역에 액세스할 수 있습니다.  

대부분의 시나리오에서 기능은 작동하기 위해 ExpressRoute 서비스 공급자를 활용하는 회로와 동일합니다. 추가 세분성 및 ExpressRoute Direct를 사용하여 제공되는 새로운 기능을 지원하기 위해 ExpressRoute Direct 회로에 존재하는 특정 키 기능이 있습니다.

## <a name="circuit-skus"></a>회로 SKU

ExpressRoute Direct는 Azure Storage 및 기타 빅 데이터 서비스에 대규모 데이터 수집 시나리오를 지원합니다. 100Gbps ExpressRoute Direct의 ExpressRoute 회로는 이제 **40Gbps** 및 **100Gbps** 회로 SKU도 지원합니다. 실제 포트 쌍은 **100Gbps 또는 10Gbps** 뿐이며 다수의 가상 회로를 가질 수 있습니다. 회로 크기:

| **100Gbps ExpressRoute Direct** | **10Gbps ExpressRoute Direct** | 
| --- | --- |
| **구독 대역폭**: 200Gbps | **구독 대역폭**: 20Gbps |
| <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> | <ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>

## <a name="technical-requirements"></a>기술적인 요구 사항

* Microsoft Enterprise Edge Router(MSEE) 인터페이스:
    * 이중 10기가비트 또는 100기가비트 이더넷 포트, 라우터 쌍 간에만 지원
    * 단일 모드 LR 파이버 연결
    * IPv4 및 IPv6
    * IP MTU 1,500바이트

* 스위치/라우터 계층 2/계층 3 연결:
    * 1개의 802.1Q(Dot1Q) 태그 또는 2개의 Tag 802.1Q(QinQ) 태그 캡슐화를 지원해야 함
    * Ethertype = 0x8100
    * Microsoft에서 지정한 VLAN ID에 따라 외부 VLAN 태그(STAG)를 추가해야 합니다(‘QinQ에만 해당’).
    * 포트 및 디바이스당 다수의 BGP 세션(VLAN)을 지원해야 합니다.
    * IPv4 및 IPv6 연결 ‘IPv6의 경우 추가 하위 인터페이스가 생성되지 않습니다. IPv6 주소는 기존 하위 인터페이스에 추가됩니다’. 
    * 선택 사항: ExpressRoute 회로의 모든 개인 피어링에 대해 기본적으로 구성된 [BFD(양방향 전달 검색)](./expressroute-bfd.md) 지원

## <a name="vlan-tagging"></a>VLAN 태그 지정

ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 태그 지정을 지원합니다.

* **QinQ VLAN 태그 지정** 은 ExpressRoute 회로 기준당 격리된 라우팅 도메인을 허용합니다. Azure는 회로 생성 시 동적으로 S 태그를 지정하고 이는 변경할 수 없습니다. 회로의 각 피어링(프라이빗 및 Microsoft)은 VLAN으로 고유한 C 태그를 사용합니다. C 태그는 ExpressRoute Direct 포트의 회로에서 고유할 필요가 없습니다.

* **Dot1Q VLAN 태그 지정** 은 각 ExpressRoute Direct 포트 쌍 기준당 단일 태그가 지정된 VLAN을 허용합니다. 피어링에 사용되는 C 태그는 ExpressRoute Direct 포트 쌍의 모든 회로 및 피어링에서 고유해야 합니다.

## <a name="workflows"></a>워크플로

### <a name="set-up-expressroute-direct"></a>Express 경로 직접 설정

:::image type="content" source="./media/expressroute-erdirect-about/set-up-workflow.png" alt-text="Express 경로 직접 설정 워크플로의 다이어그램입니다." lightbox="./media/expressroute-erdirect-about/set-up-workflow-expanded.png":::

### <a name="delete-expressroute-direct"></a>Express 경로 직접 삭제

:::image type="content" source="./media/expressroute-erdirect-about/delete-workflow.png" alt-text="Express 경로 직접 삭제 워크플로의 다이어그램입니다." lightbox="./media/expressroute-erdirect-about/delete-workflow-expanded.png":::

## <a name="sla"></a>SLA

ExpressRoute Direct는 Microsoft 글로벌 네트워크에 대한 활성/활성 중복 연결과 동일한 엔터프라이즈급 SLA를 제공합니다. ExpressRoute 인프라는 중복되며, Microsoft 글로벌 네트워크로 연결은 중복되고 다양하며 고객 요구 사항에 적절하게 크기를 조정합니다.

## <a name="pricing"></a>가격 책정

Express 경로 다이렉트의 요금 청구 방법에 대 한 자세한 내용은 [express 경로 FAQ](expressroute-faqs.md#when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs)를 참조 하세요. 가격 책정에 대 한 자세한 내용은 [express 경로 가격](https://azure.microsoft.com/pricing/details/expressroute/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Express 경로 다이렉트를 구성](expressroute-howto-erdirect.md)하는 방법을 알아봅니다.
