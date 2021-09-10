---
title: 네트워크 격리를 위한 Azure 서비스의 가상 네트워크 통합
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure 서비스에 안전하게 액세스할 수 있는 가상 네트워크에 Azure 서비스를 통합하는 다양한 방법을 설명합니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 8630451fe4ff8b3468b5c31168a417a72e8769f3
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386285"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>네트워크 격리를 위해 가상 네트워크와 Azure 서비스 통합

Azure 서비스에 대한 VNet(가상 네트워크) 통합을 사용하면 가상 네트워크 인프라로만 서비스에 대한 액세스를 잠글 수 있습니다. VNet 인프라에는 피어링된 가상 네트워크와 온-프레미스 네트워크도 포함됩니다.

VNet 통합은 Azure 서비스에 네트워크 격리의 이점을 제공하고 다음 방법 중 하나 이상을 통해 수행할 수 있습니다.
- [서비스의 전용 인스턴스를 가상 네트워크에 배포합니다](virtual-network-for-azure-services.md). 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- [Azure Private Link](../private-link/private-link-overview.md)가 제공하는, 서비스에 비공개로 안전하게 연결하는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용합니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 가상 네트워크로 효과적으로 가져옵니다.
- [서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 통해 가상 네트워크를 서비스로 확장하여 퍼블릭 엔드포인트를 사용하여 서비스에 액세스합니다. 서비스 엔드포인트를 통해 서비스 리소스를 가상 네트워크로 보호할 수 있습니다.
- [서비스 태그](service-tags-overview.md)를 사용하여 공용 IP 엔드포인트에 대한 Azure 리소스의 트래픽을 허용하거나 거부합니다.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>가상 네트워크에 전용 Azure 서비스 배포

가상 네트워크에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 전용 Azure 서비스 배포](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

전용 Azure 서비스를 가상 네트워크에 배포하면 다음과 같은 기능이 제공됩니다.
- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 사이트 간 VPN(VPN Gateway) 또는 ExpressRoute를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 피어링하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스를 통해 완전히 관리됩니다. 이 관리에는 리소스의 상태 모니터링 및 로드에 따른 스케일링이 포함됩니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대한 인바운드 및 아웃바운드 네트워크 액세스는 서비스에서 제공하는 지침에 따라 네트워크 보안 그룹을 통해 열어야 합니다.
- 특정 서비스는 배포된 서브넷에 제한을 가합니다. 이러한 제한은 동일한 서브넷 내에서 정책의 적용, 경로 또는 VM과 서비스 리소스의 결합을 제한합니다. 시간이 지남에 따라 변경될 수 있으므로 특정 제한 사항에 대해서는 각 서비스에 문의하세요. 이러한 서비스의 예로는 Azure NetApp Files, 전용 HSM, Azure Container Instances, App Service가 있습니다.
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 위임된 서브넷을 요구할 수 있습니다. 위임하면 서비스는 위임된 서브넷에서 서비스별 리소스를 만들 수 있는 명시적 권한을 부여합니다.
- 위임된 서브넷이 있는 가상 네트워크에서 REST API 응답의 예를 참조하세요. 위임된 서브넷 모델을 사용하는 포괄적인 서비스 목록은 사용 가능한 위임 API를 통해 얻을 수 있습니다.

가상 네트워크에 배포할 수 있는 서비스 목록은 [가상 네트워크에 전용 Azure 서비스 배포](virtual-network-for-azure-services.md)를 참조하세요.

## <a name="private-link-and-private-endpoints"></a>프라이빗 링크 및 프라이빗 엔드포인트

프라이빗 엔드포인트를 사용하면 가상 네트워크에서 Azure 리소스로 안전하게 트래픽을 수신할 수 있습니다. 이 프라이빗 링크는 공용 IP 주소 없이 설정됩니다. 프라이빗 엔드포인트는 가상 네트워크의 Azure 서비스에 대한 특별한 네트워크 인터페이스입니다. 리소스에 대한 프라이빗 엔드포인트를 만들면 가상 네트워크의 클라이언트와 Azure 리소스 간에 보안 연결이 제공됩니다. 프라이빗 엔드포인트에는 가상 네트워크의 IP 주소 범위에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 Azure 서비스 간의 연결은 프라이빗 링크입니다.

다이어그램에서 오른쪽은 대상 PaaS 서비스로서의 Azure SQL Database를 보여 줍니다. 대상은 [프라이빗 엔드포인트를 지원하는 서비스](../private-link/availability.md)일 수 있습니다. 여러 고객에 대한 논리적 SQL Server의 여러 인스턴스가 있으며 모두 공용 IP 주소를 통해 연결할 수 있습니다.

이 경우 논리적 SQL Server의 인스턴스 하나가 프라이빗 엔드포인트와 함께 노출됩니다. 엔드포인트는 클라이언트의 가상 네트워크에 있는 개인 IP 주소를 통해 SQL Server에 연결할 수 있게 합니다. DNS 구성의 변경으로 인해 클라이언트 애플리케이션은 이제 해당 트래픽을 해당 프라이빗 엔드포인트로 직접 보냅니다. 대상 서비스에는 VNet의 개인 IP 주소에서 시작된 트래픽이 표시됩니다. 

프라이빗 링크는 녹색 화살표로 표시됩니다. 프라이빗 엔드포인트와 함께 대상 리소스에 대한 공용 IP 주소가 여전히 ‘존재’할 수 있습니다. 공용 IP는 클라이언트 애플리케이션에서 더 이상 사용되지 않습니다. 이제 방화벽은 해당 공용 IP 주소에 대한 액세스를 허용하지 않을 수 있으며, 이 경우 프라이빗 엔드포인트를 ‘통해서만’ 액세스가 가능합니다. VNet에서 프라이빗 엔드포인트 없이 SQL Server에 연결하는 것은 여전히 공용 IP 주소에서 시작됩니다. 이 흐름은 파란색 화살표로 표시됩니다.

![프라이빗 엔드포인트](./media/network-isolation/architecture-private-endpoints.png)

클라이언트 애플리케이션은 일반적으로 DNS 호스트 이름을 사용하여 대상 서비스에 도달합니다. 애플리케이션을 변경할 필요가 없습니다. 동일한 호스트 이름을 원래 공용 IP 주소 대신 대상 리소스의 개인 IP 주소로 확인하도록 [VNet의 DNS 확인을 구성해야 합니다.](../private-link/private-endpoint-dns.md) 클라이언트와 대상 서비스 간에 프라이빗 경로를 사용하는 경우 클라이언트는 공용 IP 주소에 의존하지 않습니다. 대상 서비스는 퍼블릭 액세스를 해제할 수 있습니다.

이렇게 개별 인스턴스를 노출하여 [데이터 절도를 방지](../private-link/private-endpoint-overview.md#network-security-of-private-endpoints)할 수 있습니다. 악의적인 행위자가 데이터베이스에서 정보를 수집하여 다른 퍼블릭 데이터베이스 또는 스토리지 계정에 업로드할 수 없습니다. ‘모든’ PaaS 서비스의 공용 IP 주소에 대한 액세스를 차단할 수 있습니다. 프라이빗 엔드포인트를 통해 PaaS 인스턴스에 대한 액세스를 계속 허용할 수 있습니다.

프라이빗 링크 및 지원되는 Azure 서비스 목록에 대한 자세한 내용은 [프라이빗 링크란?](../private-link/private-link-overview.md)을 참조하세요.

## <a name="service-endpoints"></a>서비스 엔드포인트

서비스 엔드포인트는 Azure 백본 네트워크를 통해 Azure 서비스에 대한 안전하고 직접적인 연결을 제공합니다. 엔드포인트를 사용하면 가상 네트워크에 대해서만 Azure 리소스를 보호할 수 있습니다. 서비스 엔드포인트를 사용하면 아웃바운드 공용 IP 없이도 VNet의 개인 IP 주소가 Azure 서비스에 연결할 수 있습니다.

서비스 엔드포인트가 없으면 VNet에 대한 액세스만 제한하는 것이 어려울 수 있습니다. 원본 IP 주소를 변경하거나 다른 고객과 공유할 수 있습니다. 예를 들면 공유 아웃바운드 IP 주소가 있는 PaaS 서비스입니다. 서비스 엔드포인트를 사용하면 대상 서비스에 표시되는 원본 IP 주소가 VNet의 개인 IP 주소가 됩니다. 이 수신 트래픽 변경을 통해 출처를 쉽게 식별하고 적절한 방화벽 규칙을 구성하는 데 사용할 수 있습니다. 예를 들면 해당 VNet 내의 특정 서브넷에서 오는 트래픽만 허용합니다.

서비스 엔드포인트에서 Azure 서비스의 DNS 항목은 현재 상태로 유지되고 Azure 서비스에 할당된 공용 IP 주소로 계속 사용됩니다.

아래 다이어그램에서 오른쪽은 동일한 대상 PaaS 서비스입니다. 왼쪽에는 두 개의 서브넷이 있는 고객 VNet이 있습니다. 서브넷 A에는 `Microsoft.Sql`에 대한 서비스 엔드포인트가 있고 서브넷 B에는 서비스 엔드포인트가 정의되어 있지 않습니다. 

서브넷 B의 리소스는 SQL Server에 연결하려 할 때 아웃바운드 통신에 공용 IP 주소를 사용합니다. 이 트래픽은 파란색 화살표로 표시됩니다. SQL Server 방화벽은 해당 공용 IP 주소를 사용하여 네트워크 트래픽을 허용하거나 차단해야 합니다. 

서브넷 A의 리소스는 데이터베이스 서버에 연결하려 할 때 VNet 내에서 개인 IP 주소로 표시됩니다. 이 트래픽은 녹색 화살표로 표시됩니다. 이제 SQL Server 방화벽이 서브넷 A를 구체적으로 허용하거나 차단할 수 있습니다. 원본 서비스의 공용 IP 주소에 대해서는 알 필요가 없습니다.

![서비스 엔드포인트](./media/network-isolation/architecture-service-endpoints.png)

서비스 엔드포인트는 대상 서비스의 **모든** 인스턴스에 적용됩니다. 예를 들어 고객의 인스턴스뿐만 아니라 Azure 고객의 **모든** SQL Server 인스턴스입니다.

자세한 내용은 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 참조하세요.

## <a name="service-tags"></a>서비스 태그

서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. 서비스 태그를 사용하여 [네트워크 보안 그룹](./network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. 트래픽을 허용하거나 거부하려면 규칙의 원본 또는 대상 필드에 서비스 태그를 지정합니다. 

![서비스 태그를 사용하여 트래픽 허용 또는 거부](./media/network-isolation/service-tags.png)

퍼블릭 엔드포인트가 있는 Azure 서비스에 액세스하면서, 네트워크 격리를 수행하고 인터넷에서 Azure 리소스를 보호할 수 있습니다. 인바운드/아웃바운드 네트워크 보안 그룹 규칙을 만들어 **인터넷** 과의 트래픽을 거부하고 **AzureCloud** 와의 트래픽을 허용합니다. 추가 서비스 태그는 특정 Azure 서비스의 [사용 가능한 서비스 태그](service-tags-overview.md#available-service-tags)를 참조하세요.

서비스 태그와 이를 지원하는 Azure 서비스에 대한 자세한 내용은 [서비스 태그 개요](service-tags-overview.md)를 참조하세요.

## <a name="compare-private-endpoints-and-service-endpoints"></a>프라이빗 엔드포인트와 서비스 엔드포인트 비교

>[!NOTE]
> Microsoft는 Azure Private Link를 사용할 것을 권장합니다. Private Link는 기본 제공된 데이터 반출 방지 및 자체 네트워크의 Private IP에 대한 매핑 서비스에 있는 온-프레미스에서 PaaS에 전용으로 액세스하는 것과 같은 더 나은 기능을 제공합니다. 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md)를 참조하세요.

차이점만 살펴보는 대신 서비스 엔드포인트와 프라이빗 엔드포인트에 공통적인 특징이 있다는 점을 명심해야 합니다.

두 기능 모두 대상 서비스의 방화벽을 좀 더 세부적으로 제어하는 데 사용됩니다. 예를 들어 SQL Server 데이터베이스 또는 스토리지 계정에 대한 액세스를 제한합니다. 이전 섹션에서 자세히 설명한 대로 두 기능에 대해 작업이 서로 다릅니다.

두 방법 모두 [SNAT(Source Network Address Translation) 포트 고갈](../load-balancer/load-balancer-outbound-connections.md#scenarios) 문제를 해결합니다. NVA(네트워크 가상 어플라이언스) 또는 SNAT 포트 제한이 있는 서비스를 통해 트래픽을 터널링하는 경우 고갈이 발생할 수 있습니다. 서비스 엔드포인트 또는 프라이빗 엔드포인트를 사용하는 경우 트래픽은 대상 서비스에 대해 직접 최적화된 경로를 사용합니다. 두 기능 모두 대기 시간과 비용을 줄일 수 있으므로 대역폭을 많이 사용하는 애플리케이션에 유용할 수 있습니다.

두 경우 모두 대상 서비스에 대한 트래픽이 네트워크 방화벽 또는 NVA를 통과하는지 확인할 수 있습니다. 이 절차는 두 방법에서 서로 다릅니다. 서비스 엔드포인트를 사용하는 경우 원본 서비스를 배포하는 서브넷이 아닌 **방화벽** 서브넷에서 서비스 엔드포인트를 구성해야 합니다. 프라이빗 엔드포인트를 사용하는 경우 프라이빗 엔드포인트의 IP 주소에 대한 UDR(사용자 정의 경로)을 **원본** 서브넷에 배치합니다. 프라이빗 엔드포인트의 서브넷이 아닙니다.

차이점을 비교하고 파악하려면 아래 표를 참조하요.

| 고려 사항                                                                                                                                    | 서비스 엔드포인트                                                                                                           | 프라이빗 엔드포인트                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 구성이 적용되는 수준의 서비스 범위                                                                                                   | 전체 서비스(예: ‘모든’ SQL Server 또는 ‘모든’ 고객의 스토리지 계정)                                       | 개별 인스턴스(예: ‘자신’이 소유한 특정 SQL Server 인스턴스 또는 스토리지 계정)                                                                    |
|기본 제공된 데이터 반출 보호 - 악의적인 내부자에 의해 보호된 PaaS 리소스에서 보호되지 않는 다른 PaaS 리소스로 데이터를 이동/복사하는 기능| 아니요 | 예|
|온-프레미스에서 PaaS 리소스에 대한 프라이빗 액세스| 아니요| 예|
|서비스 액세스에 필요한 NSG 구성| 예(서비스 태그 사용)| No|
| 공용 IP 주소를 사용하지 않고 서비스에 연결할 수 있음                                                                                       | 아니요                                                                                                                          | 예                                                                                                                                                               |
| Azure 간 트래픽이 Azure 백본 네트워크에 유지됨                                                                                       | 예                                                                                                                         | 예                                                                                                                                                               |
| 서비스에서 공용 IP 주소를 사용하지 않도록 설정할 수 있음                                                                                                        | 아니요                                                                                                                          | 예                                                                                                                                                               |
| Azure Virtual Network에서 들어오는 트래픽을 쉽게 제한할 수 있음                                                                             | 예(특정 서브넷에서의 액세스를 허용하거나 NSG를 사용)                                                                   | 아니요*                                                                                                                                                               |
| 온-프레미스(VPN/ExpressRoute)에서 들어오는 트래픽을 쉽게 제한할 수 있음                                                                           | 해당 없음**                                                                                                                       | 아니요*                                                                                                                                                               |
| DNS 변경 필요                                                                                                                             | 아니요                                                                                                                          | 예([DNS 구성](../private-link/private-endpoint-dns.md) 참조)                                                                 |
| 솔루션 비용에 영향을 미침                                                                                                                | 아니요                                                                                                                          | 예( [프라이빗 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 참조)                                                                       |
| 솔루션의 [복합 SLA](/azure/architecture/framework/resiliency/business-metrics#composite-slas) 에 영향을 미침 | 아니요                                                                                                                          | 예(프라이빗 링크 서비스 자체는 [99.99% SLA](https://azure.microsoft.com/support/legal/sla/private-link/) 제공)                                                 |

*프라이빗 엔드포인트에 대한 네트워크 시야가 있는 모든 항목에는 네트워크 수준 액세스 권한이 있습니다. 이 액세스 권한은 프라이빗 엔드포인트의 NSG에서 제어할 수 없습니다.

**가상 네트워크에 대해 보호되는 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려면 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)를 허용하세요. Azure 서비스 리소스에 대한 IP 방화벽 구성을 통해 해당 IP 주소를 추가할 수 있습니다. 자세한 내용은 [VNet FAQ](virtual-networks-faq.md#can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 네트워크와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)하는 방법에 대해 알아봅니다.
- [서비스 태그를 사용하여 리소스에 대한 액세스를 제한](tutorial-restrict-network-access-to-resources.md)하는 방법에 대해 알아봅니다.
- [Azure Private Link를 사용하여 Azure Cosmos 계정에 비공개로 연결](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)하는 방법을 알아봅니다.
