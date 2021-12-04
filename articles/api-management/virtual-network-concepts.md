---
title: Azure 가상 네트워크를 사용하여 Azure API Management
description: API Management 인스턴스를 Azure 가상 네트워크에 연결하기 위한 시나리오 및 요구 사항에 대해 알아봅니다.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 7938fdef0ea451f2631756be99325b6362cce091
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543383"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>Azure API Management 가상 네트워크 사용

Azure VNET(Virtual Network)를 사용하면 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 Azure 리소스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 VNET을 온-프레미스 네트워크에 연결할 수 있습니다. Azure VNET에 대해 자세히 알아보려면 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)의 내용부터 참조하세요.

이 문서에서는 API Management 인스턴스에 대한 VNET 연결 옵션, 요구 사항 및 고려 사항을 설명합니다. 배포에 Azure Portal, Azure CLI, Azure Resource Manager 템플릿 또는 기타 도구를 사용할 수 있습니다. 네트워크 보안 그룹을 사용하여 API Management 배포되는 서브넷으로의 인바운드 및 아웃바운드 [트래픽을 제어합니다.][NetworkSecurityGroups]

자세한 배포 단계 및 네트워크 구성은 다음을 참조하세요.

* [Azure API Management 를 사용하여 외부 가상 네트워크에 커넥트.](./api-management-using-with-vnet.md)
* [Azure API Management 사용하여 내부 가상 네트워크에 커넥트.](./api-management-using-with-internal-vnet.md)

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>액세스 옵션

기본적으로 API Management 인스턴스는 인터넷에서 액세스할 수 있어야 합니다. 가상 네트워크를 사용하여 개발자 포털, API 게이트웨이 및 기타 API Management 엔드포인트를 인터넷(외부 모드) 또는 VNET(내부 모드) 내에서만 액세스할 수 있도록 구성할 수 있습니다. 

* **외부** - API Management 엔드포인트는 외부 부하 분산 장치에서 공용 인터넷에서 액세스할 수 있습니다. 게이트웨이에서 VNET 내의 리소스에 액세스할 수 있습니다.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="외부 VNET에 커넥트":::

    외부 모드의 API Management 사용하여 가상 네트워크에 배포된 백 엔드 서비스에 액세스합니다.

* **내부** - API Management 엔드포인트는 내부 부하 분산을 통해 VNET 내에서만 액세스할 수 있습니다. 게이트웨이에서 VNET 내의 리소스에 액세스할 수 있습니다.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="내부 VNET에 커넥트":::

    내부 모드에서 API Management를 사용하여 다음을 수행합니다.

    * Azure VPN 연결 또는 Azure ExpressRoute 사용하여 타사에서 프라이빗 데이터 센터에 호스트된 API에 안전하게 액세스할 수 있도록 합니다.
    * 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
    * 단일 게이트웨이 엔드포인트를 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다.


## <a name="network-resource-requirements"></a>네트워크 리소스 요구 사항

다음은 API Management 대한 가상 네트워크 리소스 요구 사항입니다. 일부 요구 사항은 `stv2` `stv1` API Management 인스턴스를 호스트하는 [컴퓨팅 플랫폼의](compute-infrastructure.md) 버전( 또는 )에 따라 다릅니다.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Azure Resource Manager 가상 네트워크가 필요합니다.
* 가상 네트워크 및 서브넷을 지정하는 것 외에도 표준 SKU [공용 IPv4 주소를](../virtual-network/ip-services/public-ip-addresses.md#sku) 제공해야 합니다.
* API Management 인스턴스에 연결하는 데 사용되는 서브넷에는 다른 Azure 리소스 종류가 포함될 수 있습니다.
* 위의 서브넷에 연결된 [네트워크 보안 그룹입니다.](../virtual-network/network-security-groups-overview.md) API Management 내부적으로 사용되는 부하 분산기는 기본적으로 안전하며 모든 인바운드 트래픽을 거부하므로 NSG(네트워크 보안 그룹)는 인바운드 연결을 명시적으로 허용해야 합니다. 
* API Management 서비스, 가상 네트워크 및 서브넷 및 공용 IP 주소 리소스는 동일한 지역 및 구독에 있어야 합니다.
* 다중 지역 API Management 배포의 경우 각 위치에 대해 가상 네트워크 리소스를 개별적으로 구성합니다.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Azure Resource Manager 가상 네트워크가 필요합니다.
* API Management 인스턴스에 연결하는 데 사용되는 서브넷은 API Management 전용이어야 합니다. 다른 Azure 리소스 종류는 포함할 수 없습니다.
* API Management 서비스, 가상 네트워크 및 서브넷 리소스는 동일한 지역 및 구독에 있어야 합니다.
* 다중 지역 API Management 배포의 경우 각 위치에 대해 가상 네트워크 리소스를 개별적으로 구성합니다.

---

## <a name="subnet-size"></a>서브넷 크기

API Management 배포할 수 있는 서브넷의 최소 크기는 /29이며, 세 개의 사용 가능한 IP 주소를 제공합니다. API Management의 추가 배율 단위마다 두 개의 IP 주소가 더 필요합니다. 최소 크기 요구 사항은 다음과 같은 고려 사항을 기반으로 합니다.

* Azure는 사용할 수 없는 각 서브넷 내에서 일부 IP 주소를 예약합니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 적합성을 위해 예약되어 있습니다. Azure 서비스에는 세 개의 주소가 더 사용됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)를 참조하세요.

* Azure VNET 인프라에 사용되는 IP 주소 외에도, 서브넷의 각 API Management 인스턴스는 다음을 사용합니다.
    * 프리미엄 SKU 단위당 두 개의 IP 주소 
    * 개발자 SKU에 대해 하나의 IP 주소 

* 각 인스턴스는 외부 부하 분산 장치에 대해 하나의 IP 주소를 추가로 예약합니다. [내부 VNET](./api-management-using-with-internal-vnet.md)에 배포하는 경우 인스턴스에는 내부 부하 분산에 대한 추가 IP 주소가 필요합니다.

## <a name="routing"></a>라우팅

[외부 VNET](./api-management-using-with-vnet.md#routing) 또는 내부 [VNET에](./api-management-using-with-internal-vnet.md#routing)API Management 인스턴스를 배포할 때 라우팅 지침을 참조하세요.

API Management IP [주소에](api-management-howto-ip-addresses.md)대해 자세히 알아봅니다.

## <a name="dns"></a>DNS

외부 모드에서 VNET은 API Management 엔드포인트 및 기타 Azure 리소스에 대해 Azure [제공 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 기능을 사용하도록 설정합니다. 온-프레미스 리소스에 대한 이름 확인은 제공하지 않습니다. 

내부 모드에서는 API Management 엔드포인트 및 기타 필요한 Azure 리소스에 대한 이름 확인이 보장되도록 자체 DNS 솔루션을 제공해야 합니다. Azure 프라이빗 DNS [영역](../dns/private-dns-overview.md)을 구성하는 것이 좋습니다.

자세한 내용은 다음을 참조하세요. 
* [Azure 가상 네트워크에서 리소스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).  
* [Azure 프라이빗 DNS 영역 만들기](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> VNET에 사용자 지정 DNS 솔루션을 사용하려는 경우 API Management 서비스를 **배포하기 전에** 설정합니다. 그렇지 않으면 [네트워크 구성 적용 작업을](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates)실행하여 DNS 서버를 변경할 때마다 API Management 서비스를 업데이트해야 합니다.

## <a name="limitations"></a>제한 사항

일부 제한은 `stv2` `stv1` API Management 인스턴스를 호스트하는 [컴퓨팅 플랫폼의](compute-infrastructure.md) 버전( 또는 )에 따라 다릅니다.

### <a name="stv2"></a>[stv2](#tab/stv2)

* API Management 인스턴스를 포함하는 서브넷은 구독 간에 이동할 수 없습니다.
* 내부 VNET 모드에서 구성된 다중 지역 API Management 배포의 경우 사용자가 라우팅을 소유하며, 여러 지역 사이에서 부하 분산을 관리할 책임이 있습니다.
* [OpenAPI 사양](import-and-publish.md)에서 API Management API를 가져오려면 사양 URL을 공개적으로 액세스할 수 있는 인터넷 주소에서 호스트해야 합니다.

### <a name="stv1"></a>[stv1](#tab/stv1)

* API Management 인스턴스를 포함하는 서브넷은 구독 간에 이동될 수 없습니다.
* 내부 VNET 모드에서 구성된 다중 지역 API Management 배포의 경우 사용자가 라우팅을 소유하며, 여러 지역 사이에서 부하 분산을 관리할 책임이 있습니다.
* [OpenAPI 사양](import-and-publish.md)에서 API Management API를 가져오려면 사양 URL을 공개적으로 액세스할 수 있는 인터넷 주소에서 호스트해야 합니다.
* 다른 지역에서 전역적으로 피어링된 VNET의 리소스에서 내부 모드의 API Management 서비스 간 연결은 플랫폼 제한 때문에 작동하지 않습니다. 자세한 내용은 가상 [네트워크 설명서 를 참조하세요.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

---

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [VPN Gateway 사용하여 백 엔드에 가상 네트워크 연결](../vpn-gateway/design.md#s2smulti)
* [다른 배포 모델에서 가상 네트워크 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [가상 네트워크 질문과 대답](../virtual-network/virtual-networks-faq.md)

가상 네트워크에 커넥트:
* [Azure API Management 를 사용하여 외부 가상 네트워크에 커넥트.](./api-management-using-with-vnet.md)
* [Azure API Management 사용하여 내부 가상 네트워크에 커넥트.](./api-management-using-with-internal-vnet.md)

다음 항목을 검토합니다.

* [VPN Gateway를 사용하여 Virtual Network를 백 엔드에 연결](../vpn-gateway/design.md#s2smulti)
* [다양한 배포 모델에서 Virtual Network 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [API 검사기를 사용하여 Azure API Management에서 호출을 추적하는 방법](api-management-howto-api-inspector.md)
* [가상 네트워크 FAQ(질문과 대답)](../virtual-network/virtual-networks-faq.md)
* [서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
