---
title: 'Azure ExpressRoute: 퍼블릭 피어링을 Microsoft 피어링으로 이동'
description: 이 문서에서는 ExpressRoute에서 공용 피어링을 Microsoft 피어링으로 이동하는 단계를 보여 줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/28/2021
ms.author: duau
ms.openlocfilehash: be671ec7a1f2e8fb11c49b8d17290c88f5db254a
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203382"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>공용 피어링을 Microsoft 피어링으로 이동

이 문서에서는 가동 중지 없이 공용 피어링 구성을 Microsoft 피어링으로 이동할 수 있습니다. ExpressRoute는 Azure 스토리지 및 Azure SQL Database와 같은 Azure PaaS 서비스에 대한 경로 필터를 사용하여 Microsoft 피어링 사용을 지원합니다. Microsoft PaaS 및 SaaS 서비스에 액세스하려면 하나의 라우팅 도메인만 있으면 됩니다. 경로 필터를 사용하여 사용하려는 Azure 지역에 대한 PaaS 서비스 접두사를 선택적으로 보급할 수 있습니다.

Azure 퍼블릭 피어링에서는 각 BGP 세션에 NAT IP 주소 1개가 연결됩니다. Microsoft 피어링을 사용하면 사용자 고유의 NAT 할당을 구성하고 선택적 접두사 보급에 경로 필터를 사용할 수 있습니다. 퍼블릭 피어링은 항상 WAN에서 Microsoft Azure 서비스로 연결이 시작되는 단방향 서비스입니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다.

공용 피어링을 사용하도록 설정하면 모든 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다. 반면에 Microsoft 피어링은 WAN과 함께 Microsoft Azure 서비스에서 연결을 시작할 수 있는 양방향 연결입니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

Microsoft 피어링에 연결하려면 NAT를 설정하고 관리해야 합니다. 연결 공급자에서 NAT를 관리 서비스로 설정하고 관리할 수 있습니다. Microsoft 피어링에서 Azure PaaS 및 Azure SaaS 서비스에 액세스하려는 경우 NAT IP 풀의 크기를 올바르게 조정해야 합니다. ExpressRoute용 NAT에 대한 자세한 내용은 [Microsoft 피어링에 대한 NAT 요구 사항](expressroute-nat.md#nat-requirements-for-microsoft-peering)을 참조하세요. Azure ExpressRoute(Microsoft 피어링)를 통해 Microsoft에 연결하는 경우 여러 개의 Microsoft 링크를 사용할 수 있습니다. 한 가지 링크는 기존 인터넷 연결이고 다른 하나는 ExpressRoute를 통한 연결입니다. Microsoft에 대한 일부 트래픽은 인터넷을 통과할 수 있지만 ExpressRoute를 통해 돌아옵니다. 혹은 그 반대입니다.

![양방향 연결](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Microsoft에 보급된 NAT IP 풀은 인터넷에 보급되지 않아야 합니다. 다른 Microsoft 서비스에 대한 연결을 중단합니다.

Microsoft 피어링을 구성하기 전에 [여러 네트워크 경로를 사용하는 비대칭 라우팅](./expressroute-asymmetric-routing.md)에서 비대칭 라우팅의 주의 사항을 확인하세요.

* 공용 피어링을 사용하고 있고 현재 [Azure Storage](../storage/common/storage-network-security.md)나 [Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md)에 액세스하는 데 사용되는 공용 IP 주소에 대한 IP 네트워크 규칙이 있는 경우, Microsoft 피어링으로 구성된 NAT IP 풀이 Azure 스토리지 계정 또는 Azure SQL 계정의 공용 IP 주소 목록에 포함되어 있는지 확인해야 합니다.
* 레거시 공용 피어링은 Microsoft 등록 공용 IP에 대한 SNAT(원본 네트워크 주소 변환)를 사용하지만 Microsoft 피어링은 사용하지 않습니다.
* 가동 중지 없이 Microsoft 피어링으로 이동하려면 이 문서의 단계를 제시된 순서대로 사용하십시오.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Microsoft 피어링 만들기

Microsoft 피어링을 만들지 않은 경우 다음 문서 중 하나를 사용하여 Microsoft 피어링을 만듭니다. 연결 공급자에서 3계층 관리 서비스를 제공하는 경우 회로에 대해 Microsoft 피어링을 사용하도록 연결 공급자에 요청할 수 있습니다.

계층 3을 직접 관리하는 경우에는 계속 진행하기 전에 다음 정보가 필요합니다.

* 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.<br>
* 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.<br>
* 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.<br>
* 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.<br>
* 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.<br>
* 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.

* **선택 사항** - 고객 ASN: 피어링 AS 번호에 등록되지 않은 접두사를 보급하는 경우 접두사가 등록된 AS 번호를 지정할 수 있습니다.<br>
* **선택 사항** - 사용하기로 선택한 경우 MD5 해시.

Microsoft 피어링을 사용하도록 설정하는 방법에 대한 자세한 지침은 다음 문서에서 확인할 수 있습니다.

* [Azure Portal을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Azure Powershell을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-arm.md#msft)<br>
* [Azure CLI를 사용하여 Microsoft 피어링 만들기](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Microsoft 피어링이 사용하도록 설정되었는지 확인

Microsoft 피어링을 사용하도록 설정되고 보급된 공용 접두사가 구성되어 있는지 확인합니다.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. 경로 필터 구성 및 회로에 연결

기본적으로 새 Microsoft 피어링은 경로 필터가 회로에 연결될 때까지 어떤 접두사도 보급하지 않습니다. 경로 필터 규칙을 만들 때 Azure PaaS 서비스에 사용할 Azure 지역의 서비스 커뮤니티 목록을 지정할 수 있습니다. 이렇게 하면 다음 스크린샷에 표시된 것처럼 요구 사항에 따라 경로를 유연하게 필터링할 수 있습니다.

![공용 피어링 병합](./media/how-to-move-peering/routefilter.jpg)

다음 문서 중 하나를 사용하여 경로 필터를 구성합니다.

* [Azure Portal을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-portal.md)<br>
* [Azure PowerShell을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-powershell.md)<br>
* [Azure CLI를 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. 퍼블릭 피어링 삭제

Microsoft 피어링이 구성되어 있고 사용하려는 접두사가 Microsoft 피어링에서 올바르게 보급되었는지 확인한 후에는 공용 피어링을 삭제할 수 있습니다. 공용 피어링을 삭제하려면 다음 문서 중 하나를 사용합니다.

* [Azure PowerShell을 사용하여 Azure 공용 피어링 삭제](about-public-peering.md#powershell)
* [CLI를 사용하여 Azure 공용 피어링 삭제](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. 피어링 보기
  
Azure Portal에서 모든 ExpressRoute 회로 및 피어링 목록을 확인할 수 있습니다. 자세한 내용은 [Microsoft 피어링 세부 정보 보기](expressroute-howto-routing-portal-resource-manager.md#getmsft)를 참조하세요.

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
