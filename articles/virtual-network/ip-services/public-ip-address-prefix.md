---
title: 공용 IP 주소 접두사
titleSuffix: Azure Virtual Network
description: Azure 공용 IP 주소 접두사가 무엇인지, 공용 IP 주소를 리소스에 할당하는 데 어떻게 도움이 되는지 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 3d50a7246fcbf8d9a9ccbd2985df9b3a70ed18ab
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133109836"
---
# <a name="public-ip-address-prefix"></a>공용 IP 주소 접두사

공용 IP 주소 접두사는 Azure에서 예약된 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses) 범위입니다. 공용 IP 주소 접두사는 각 Azure 지역의 주소 풀에서 할당됩니다. 이름 및 접두사 크기를 지정하여 Azure 지역 및 구독에서 공용 IP 주소 접두사를 만듭니다. 접두사 크기는 사용할 수 있는 주소 수입니다. 공용 IP 주소 접두사는 IPv4 또는 IPv6 주소로 구성됩니다.  가용성 영역이 있는 지역에서는 공용 IP 주소 접두사는 영역 중복으로 만들거나 특정 가용성 영역에 연결할 수 있습니다.  공용 IP 주소 접두사를 만든 후 공용 IP 주소를 만들 수 있습니다.

## <a name="benefits"></a>이점

- 알려진 범위에서 정적 공용 IP 주소 리소스를 만듭니다. 접두사를 사용하여 만든 주소는 표준 SKU 공용 IP 주소를 할당할 수 있는 Azure 리소스에 할당할 수 있습니다.
- 개별 공용 IP를 삭제하면 나중에 재사용할 수 있도록 예약된 범위로 *반환* 됩니다. 공용 IP 주소 접두사의 IP 주소는 접두사를 삭제할 때까지 사용하도록 예약됩니다.
- 접두사 범위 내에서 어떤 IP 주소가 할당되고 사용할 수 있는지 볼 수 있습니다.

## <a name="prefix-sizes"></a>접두사 크기

다음 공용 IP 접두사 크기를 사용할 수 있습니다.

-  /28(IPv4) 또는 /124(IPv6) = 16개 주소
-  /29(IPv4) 또는 /125(IPv6) = 8개 주소
-  /30(IPv4) 또는 /126(IPv6) = 4개 주소
-  /31(IPv4) 또는 /127(IPv6) = 2개 주소

접두사 크기는 CIDR(Classless Inter-Domain Routing) 마스크 크기로 지정됩니다.

구독에서 만든 접두사 수에 대한 제한은 없습니다. 만든 범위의 수는 구독에 허용할 수 있는 정적 공용 IP 주소 수를 초과할 수 없습니다. 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="scenarios"></a>시나리오
접두사의 고정 공용 IP 주소에는 다음과 같은 리소스를 연결할 수 있습니다.

|리소스|시나리오|단계|
|---|---|---|
|가상 머신| Azure의 가상 머신에 공용 Ip를 연결 하면 방화벽의 allowlist에 IP 주소를 추가할 때 관리 오버 헤드가 줄어듭니다. 하나의 방화벽 규칙에서 전체 접두사를 허용 목록에 포함시킬 수 있습니다. Azure에서 가상 머신으로 확장할 때 동일한 접두사의 IP를 연결하면 비용, 시간 및 관리 오버헤드를 절약할 수 있습니다.| 접두사의 IP를 가상 머신에 연결하려면: </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 3. [IP를 가상 머신의 네트워크 인터페이스에 연결합니다.](./virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [가상 머신 확장 집합에 IP를 연결](https://azure.microsoft.com/resources/templates/vmss-with-public-ip-prefix/)할 수도 있습니다.
| 표준 부하 분산 장치 | 접두사의 공용 IP를 프런트 엔드 IP 구성 또는 부하 분산 장치의 아웃바운드 규칙에 연결하면 Azure 공용 IP 주소 공간이 간소화됩니다. 인접한 IP 주소 범위에서 아웃 바운드 연결을 제거하여 시나리오를 간소화 합니다. | 접두사의 IP를 부하 분산 장치에 연결하려면: </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 부하 분산 장치를 만들 때 위의 2단계에서 부하 분산 장치의 프런트 엔드 IP로 만든 IP를 선택하거나 업데이트합니다. |
| Azure Firewall | 아웃바운드 SNAT의 접두사에서 공용 IP를 사용할 수 있습니다. 모든 아웃바운드 가상 네트워크 트래픽은 [Azure Firewall](../../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 공용 IP로 변환됩니다. | IP를 접두사에서 방화벽에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> [Azure Firewall을 배포](../../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)할 때 접두사에서 이전에 할당한 IP를 반드시 선택해야 합니다.|
| VPN Gateway(AZ SKU), Application Gateway v2, NAT Gateway | 게이트웨이의 접두사에서 공용 IP를 사용할 수 있습니다. | IP를 접두사에서 게이트웨이에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md), [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway) 또는 [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md)를 배포할 때 이전에 접두사에서 제공한 IP를 선택해야 합니다.|

또한 공용 IP 주소 접두사 리소스는 특정 리소스에서 직접 활용할 수 있습니다.

리소스|시나리오|단계|
|---|---|---|
|가상 머신 크기 집합 | 개별 공용 IP 리소스는 생성되지 않지만 공용 IP 주소 접두사를 사용하여 가상 머신 확장 집합에서 인스턴스 수준 IP를 생성할 수 있습니다. | 확장 집합 생성의 일부로 공용 IP 구성에 이 접두사를 사용하는 지침이 있는 [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vmss-with-public-ip-prefix)을 사용합니다. (출력에 표시 되지 않지만 접두사의 영역 속성은 인스턴스 Ip에 전달 됩니다. 자세한 내용은 [가상 머신 확장 집합에 대 한 네트워킹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) 을 참조 하세요.) |
| 표준 부하 분산 장치 | 공용 IP 주소 접두사는 [아웃바운드 연결 범위에서 모든 IP를 사용](../../load-balancer/outbound-rules.md#scale)하여 부하 분산 장치의 크기를 조정하는 데 사용할 수 있습니다. | 접두사를 부하 분산 장치에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. 부하 분산 장치를 만들 때 부하 분산 장치의 프런트 엔드와 연결된 IP 접두사를 선택합니다. |
| NAT Gateway | 공용 IP 접두사는 아웃바운드 연결에 대한 접두사에 공용 IP를 사용하여 NAT Gateway의 크기를 조정하는 데 사용할 수 있습니다. | 접두사를 NAT Gateway에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. NAT Gateway를 만들 때 IP 접두사를 아웃바운드 IP로 선택합니다. |
| VPN Gateway (AZ SKU) 또는 Application Gateway v2 | 영역 중복 VPN 또는 애플리케이션 게이트웨이 v2에 대해 접두사에서 공용 IP를 사용할 수 있습니다. | IP를 접두사에서 게이트웨이에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md) 또는 [애플리케이션 게이트웨이](../../application-gateway/quick-create-portal.md#create-an-application-gateway)를 배포할 때 이전에 접두사에서 제공한 IP를 선택해야 합니다.|

## <a name="limitations"></a>제한 사항

- 접두사에 대한 IP 주소를 지정할 수 없습니다. 사용자가 지정한 크기에 따라 접두사에 대한 IP 주소가 Azure에서 할당됩니다.  또한 접두사에서 만든 모든 공용 IP 주소는 접두사와 동일한 Azure 지역 및 구독에 있어야 합니다. 동일한 지역 및 구독의 리소스에 주소를 할당해야 합니다.
- 최대 16개 IP 주소의 접두사를 만들 수 있습니다. 자세한 내용은 [네트워크 제한 증가 요청](../../azure-portal/supportability/networking-quota-requests.md) 및 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- 접두사를 만든 후에는 범위의 크기를 수정할 수 없습니다.
- 표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)를 참조하세요.
- 범위에서 가져온 주소는 Azure Resource Manager 리소스에만 할당할 수 있습니다. 클래식 배포 모델의 리소스에는 주소를 할당할 수 없습니다.
- 접두사 내에 있는 주소가 리소스에 연결된 공용 IP 주소 리소스에 할당되어 있으면 접두사를 삭제할 수 없습니다. 먼저 접두사의 IP 주소가 할당되어 있는 모든 공용 IP 주소 리소스를 분리합니다. 공용 IP 주소를 분리하는 방법에 대한 자세한 내용은 [공용 IP 주소 관리](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.
- 공용 IP 주소 접두사는 현재 *인터넷* **라우팅 기본 설정** 또는 *전역* **계층**(지역 간 부하 분산의 경우)과 호환되지 않습니다.
- IPv6은 **동적** 할당이 있는 기본 공용 IP에서만 지원됩니다. 동적 할당은 Azure에서 리소스를 삭제하고 다시 배포하는 경우 IPv6 주소가 변경됨을 의미합니다. 
- 표준 IPv6 공용 IP는 정적(예약된) 할당을 지원합니다. 
- 표준 내부 부하 분산 장치는 할당된 서브넷 내에서 동적 할당을 지원합니다.  

## <a name="pricing"></a>가격 책정
 
Azure 공용 IP(개별 IP 주소 및 IP 범위) 사용과 관련된 비용은 [공용 IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 공용 IP 주소 접두사 [만들기](manage-public-ip-address-prefix.md)
