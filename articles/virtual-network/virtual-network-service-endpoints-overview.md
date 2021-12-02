---
title: Azure 가상 네트워크 서비스 엔드포인트
titlesuffix: Azure Virtual Network
description: 서비스 엔드포인트를 사용하여 가상 네트워크에서 Azure 리소스에 대한 직접 액세스를 사용하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: f29ea867a18682ae4f6973d0b3087d5acc500751
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440071"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network 서비스 엔드포인트

VNet(가상 네트워크) 서비스 엔드포인트는 Azure 백본 네트워크에서 최적화된 경로를 통해 Azure 서비스에 대한 안전한 직접 연결을 제공합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 엔드포인트를 통해 VNet의 개인 IP 주소는 VNet에 공용 IP 주소가 없어도 Azure 서비스의 엔드포인트에 연결할 수 있습니다.

   >[!NOTE]
   > Microsoft는 Azure 플랫폼에서 호스트되는 서비스에 대한 보안 및 프라이빗 액세스를 위해 Azure Private Link를 사용할 것을 권장합니다. 자세한 내용은 [Azure Private Link](../private-link/private-link-overview.md)를 참조하세요.  

서비스 엔드포인트는 다음 Azure 서비스 및 지역에서 제공됩니다. *Microsoft.\** 리소스는 괄호 안에 있습니다. 서비스에 대한 서비스 엔드포인트를 구성하는 동안 서브넷 쪽에서 이 리소스를 사용하도록 설정합니다.

**일반 공급**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): 일반적으로 모든 Azure 지역에서 전용 SQL 풀(이전의 SQL DW)에 대해 제공됩니다.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft.Sql*): 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): 일반적으로 모든 Azure 지역에서 제공됩니다.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): 일반적으로 ADLS Gen1을 사용할 수 있는 모든 Azure 지역에서 제공됩니다.
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft.Web*): 일반적으로 모든 App Service를 사용할 수 있는 모든 Azure 지역에서 제공됩니다.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft.CognitiveServices*): 일반적으로 Cognitive Services를 사용할 수 있는 모든 Azure 지역에서 제공됩니다.

**공개 미리 보기**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Azure Container Registry를 사용할 수 있는 제한된 Azure 지역에서 사용 가능한 미리 보기입니다.

최신 알림은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 확인하세요.

## <a name="key-benefits"></a>주요 이점

서비스 엔드포인트는 다음과 같은 이점을 제공합니다.

- **Azure 서비스 리소스의 보안 향상**: VNet 프라이빗 주소 공간은 겹칠 수 있습니다. 겹치는 공간을 사용하여 VNet에서 발생하는 트래픽을 고유하게 식별할 수 없습니다. 서비스 엔드포인트는 VNet ID를 서비스로 확장하여 Azure 서비스 리소스를 가상 네트워크에 안전하게 저장할 수 있는 기능을 제공합니다. 가상 네트워크에서 서비스 엔드포인트를 사용하면 가상 네트워크 규칙을 추가하여 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 규칙을 추가하면 리소스에 대한 퍼블릭 인터넷 액세스가 완전히 제거되고 가상 네트워크의 트래픽만 허용하여 보안이 향상됩니다.
- **Virtual Network의 Azure 서비스 트래픽에 대한 최적의 라우팅**: 현재 온-프레미스 및/또는 가상 어플라이언스에 인터넷 트래픽을 강제하는 가상 네트워크의 모든 경로는 Azure 서비스 트래픽이 인터넷 트래픽과 동일한 경로를 사용하도록 강제할 수도 있습니다. 서비스 엔드포인트는 Azure 트래픽에 대한 최적의 라우팅을 제공합니다. 

  엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동시킵니다. 트래픽을 Azure 백본 네트워크에 유지하면 서비스 트래픽에 영향을 주지 않고 강제 터널링을 통해 가상 네트워크의 아웃바운드 인터넷 트래픽을 계속 감사하고 모니터링할 수 있습니다. 사용자 정의 경로 및 강제 터널링에 대한 자세한 내용은 [Azure 가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조하세요.
- **관리 오버 헤드를 덜 사용하여 간단히 설정**: IP 방화벽을 통해 Azure 리소스를 보호하기 위해 가상 네트워크에서 예약된 공용 IP 주소가 더 이상 필요하지 않습니다. 서비스 엔드포인트를 설정하는 데 NAT(Network Address Translation) 또는 게이트웨이 디바이스가 필요하지 않습니다. 서브넷을 간단히 클릭하여 서비스 엔드포인트를 구성할 수 있습니다. 엔드포인트를 유지하기 위한 추가 오버헤드가 없습니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 엔드포인트는 Azure 가상 네트워크에서 구성된 서브넷에서 활성화됩니다. 프레미스에서 Azure 서비스로의 트래픽에 엔드포인트를 사용할 수 없습니다. 자세한 내용은 [온-프레미스에서 Azure 서비스 액세스 보안 유지](#secure-azure-services-to-virtual-networks)를 참조하세요.
- Azure SQL의 경우 서비스 엔드포인트는 가상 네트워크의 지역 내에서 Azure 서비스 트래픽에만 적용됩니다. Azure Storage 경우 엔드포인트는 RA-GRS(읽기 액세스 지역 중복 스토리지) 및 GRS(지역 중복 스토리지) 트래픽을 지원하기 위해 쌍을 이뤄 가상 네트워크를 배포하는 지역을 포함하도록 확장됩니다. 자세한 내용은 [Azure 쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
- Azure Data Lake Storage(ADLS) Gen 1의 경우 VNet 통합 기능은 동일한 지역 내의 가상 네트워크에서만 사용할 수 있습니다. ADLS Gen1에 대한 가상 네트워크 통합에서는 가상 네트워크와 Azure AD(Azure Active Directory) 간에 가상 네트워크 서비스 엔드포인트 보안을 사용하여 액세스 토큰에서 추가 보안 클레임을 생성하게 됩니다. 그런 다음, 이러한 클레임을 사용하여 Data Lake Storage Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다. 서비스 엔드포인트를 지원하는 서비스에 나열된 *Microsoft.AzureActiveDirectory* 태그는 ADLS Gen 1에 서비스 엔드포인트를 지원하는 데 사용됩니다. Azure AD는 기본적으로 서비스 엔드포인트를 지원하지 않습니다. Azure Data Lake Store Gen1 VNet 통합에 대한 자세한 내용은 [Azure Data Lake Storage Gen1 네트워크 보안](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

## <a name="secure-azure-services-to-virtual-networks"></a>가상 네트워크에 대한 Azure 서비스 보호

- 가상 네트워크 서비스 엔드포인트는 Azure 서비스에 가상 네트워크의 ID를 제공합니다. 가상 네트워크에서 서비스 엔드포인트를 사용하면 가상 네트워크 규칙을 추가하여 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- 현재 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. 서비스 엔드포인트에서 서비스 트래픽은 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다. 이 스위치를 사용하면 IP 방화벽에서 사용되는 예약된 공용 IP 주소가 필요 없이 서비스에 액세스할 수 있습니다.

   >[!NOTE]
   > 서비스 엔드포인트를 사용하면 서비스 트래픽에 대한 서브넷의 가상 머신 원본 IP 주소가 공용 IPv4 주소에서 프라이빗 IPv4 주소로 전환됩니다. Azure 공용 IP 주소를 사용하는 기존 Azure 서비스 방화벽 규칙은 더 이상 이 스위치에 작동하지 않습니다. 서비스 엔드포인트를 설정하기 전에 Azure 서비스 방화벽 규칙에서 이 스위치를 허용해야 합니다. 서비스 엔드포인트를 구성하는 동안 이 서브넷의 서비스 트래픽이 일시적으로 중단될 수도 있습니다. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>온-프레미스에서 Azure 서비스 액세스 보안 유지

  기본적으로 가상 네트워크에 대해 보호된 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. Azure 서비스 리소스에 대한 IP 방화벽 구성을 통해 해당 IP 주소를 추가할 수 있습니다.

  ExpressRoute: 퍼블릭 피어링 또는 프레미스의 Microsoft 피어링에 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하는 경우 사용 중인 NAT IP 주소를 식별해야 합니다. 퍼블릭 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). ExpressRoute 퍼블릭 및 Microsoft 피어링용 NAT에 대한 자세한 내용은 [ExpressRoute NAT 요구 사항](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)을 참조하세요.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>구성

- 가상 네트워크의 서브넷에서 서비스 엔드포인트를 구성합니다. 엔드포인트는 해당 서브넷 내에서 실행되는 모든 컴퓨팅 인스턴스를 사용합니다.
- 서브넷에 지원되는 모든 Azure 서비스(예: Azure Storage, Azure SQL Database)에 여러 개의 서비스 엔드포인트를 구성할 수 있습니다.
- Azure SQL Database의 경우 가상 네트워크는 Azure 서비스 리소스와 동일한 지역에 있어야 합니다. GRS 및 RA-GRS Azure Storage 계정을 사용하는 경우 기본 계정은 가상 네트워크와 동일한 지역에 있어야 합니다. 다른 모든 서비스의 경우 모든 지역의 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 
- 엔드포인트가 구성된 가상 네트워크는 Azure 서비스 리소스와 동일하거나 다른 구독에 구성될 수 있습니다. 엔드포인트를 설정하고 Azure 서비스를 보호하는 데 필요한 사용 권한에 대한 자세한 내용은 [프로비전](#provisioning)을 참조하세요.
- 지원되는 서비스의 경우 서비스 엔드포인트를 사용하여 가상 네트워크에 대한 기존 또는 새로운 리소스를 보호할 수 있습니다.

### <a name="considerations"></a>고려 사항

- 서비스 엔드포인트를 사용하도록 설정한 후에 원본 IP 주소는 해당 서브넷의 서비스와 통신할 때 공용 IPv4 주소가 아닌 개인 IPv4 주소를 사용하도록 전환됩니다. 이 전환 중에 서비스에 대한 기존의 모든 오픈 TCP 연결이 닫힙니다. 서브넷의 서비스에 서비스 엔드포인트를 사용하거나 사용하지 않도록 설정하는 경우 중요한 작업이 실행되지 않아야 합니다. 또한 IP 주소를 전환한 후에 애플리케이션이 Azure 서비스에 자동으로 연결될 수 있어야 합니다.

  IP 주소 전환은 가상 네트워크의 서비스 트래픽에만 영향을 줍니다. 가상 머신에 할당된 공용 IPv4 주소 간에 주소가 지정된 다른 모든 트래픽에는 영향이 없습니다. Azure 서비스의 경우 Azure 공용 IP 주소를 사용하는 기존 방화벽 규칙이 있는 경우 이러한 규칙은 가상 네트워크 프라이빗 주소로 전환하는 동시에 작동이 중지됩니다.
- 서비스 엔드포인트에서 Azure 서비스의 DNS 항목은 현재 상태로 유지되고 Azure 서비스에 할당된 공용 IP 주소로 계속 사용됩니다.

- 서비스 엔드포인트의 NSG(네트워크 보안 그룹):
  - 기본적으로 NSG는 아웃바운드 인터넷 트래픽을 허용하고 VNet에서 Azure 서비스로의 트래픽도 허용합니다. 이 트래픽은 서비스 엔드포인트와 그대로 계속 작동합니다. 
  - 모든 아웃바운드 인터넷 트래픽을 거부하고 특정 Azure 서비스에 대한 트래픽만 허용하려는 경우 NSG에서 [서비스 태그](./network-security-groups-overview.md#service-tags)를 사용하여 수행할 수 있습니다. NSG 규칙에서 대상으로 지원되는 Azure 서비스를 지정할 수 있습니다. 또한 Azure에서는 각 태그의 기반이 되는 IP 주소의 유지 관리도 제공합니다. 자세한 내용은 [NSG의 Azure 서비스 태그](./network-security-groups-overview.md#service-tags)를 참조하세요. 

### <a name="scenarios"></a>시나리오

- **피어링되거나 연결된 여러 가상 네트워크**: 하나의 가상 네트워크 또는 여러 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷에서 서비스 엔드포인트를 독립적으로 활성화하고 모든 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **가상 네트워크에서 Azure 서비스로 아웃바운드 트래픽 필터링**: 가상 네트워크에서 Azure 서비스로 전송된 트래픽을 검사하거나 필터링하려는 경우 해당 가상 네트워크 내에서 네트워크 가상 어플라이언스를 배포할 수 있습니다. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고 이 서브넷에 대한 Azure 서비스 리소스만을 보호할 수 있습니다. 네트워크 가상 어플라이언스 필터링을 사용하여 가상 네트워크에서 특정 Azure 리소스로의 Azure 서비스 액세스만을 제한하도록 하려는 경우 이 시나리오가 유용할 수 있습니다. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.
- **가상 네트워크에 직접 배포된 서비스에 대한 Azure 리소스 보호**: 다양한 Azure 서비스를 가상 네트워크의 특정 서브넷에 직접 배포할 수 있습니다. 관리되는 서비스 서브넷에서 서비스 엔드포인트를 설정하여 [관리되는 서비스](virtual-network-for-azure-services.md) 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Azure 가상 머신의 디스크 트래픽**: 관리 디스크 및 비관리 디스크에 대한 가상 머신 디스크 트래픽은 Azure Storage의 서비스 엔드포인트 라우팅 변경의 영향을 받지 않습니다. 이 트래픽에는 탑재 및 탑재 해제뿐만 아니라 diskIO도 포함됩니다. 서비스 엔드포인트 및 [Azure Storage 네트워크 규칙](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 통해 REST 액세스를 네트워크를 선택하도록 페이지 Blob으로 제한할 수 있습니다. 

### <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결

서비스 엔드포인트를 특정 서비스에 구성하면 서비스 엔드포인트 경로가 다음 항목에 적용되는지 유효성을 검사합니다. 
 
- 서비스 진단에서 모든 서비스 요청의 원본 IP 주소 유효성을 검사합니다. 서비스 엔드포인트에서 모든 새로운 요청은 요청의 원본 IP 주소를 가상 네트워크 개인 IP 주소로 표시하고 가상 네트워크에서 요청한 클라이언트에 할당됩니다. 엔드포인트가 없는 경우 주소는 Azure 공용 IP 주소입니다.
- 서브넷의 모든 네트워크 인터페이스에서 유효 경로를 볼 수 있습니다. 서비스에 대한 경로:
  - 각 서비스의 주소를 지정하는 구체적인 기본 경로를 표시합니다.
  - *VirtualNetworkServiceEndpoint* 의 nextHopType이 있습니다.
  - 강제 터널링 경로에 비해 서비스에 대한 직접 연결이 더 효과적임을 나타냅니다.

>[!NOTE]
> 서비스 엔드포인트 경로는 Azure 서비스의 주소 접두사에 대한 BGP 또는 UDR 경로를 재정의합니다. 자세한 내용은 [유효 경로 관련 문제 해결](diagnose-network-routing-problem.md)을 참조하세요.

## <a name="provisioning"></a>프로비전

가상 네트워크에 대한 쓰기 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다. VNet에 대한 Azure 서비스 리소스를 보호하려면 추가된 서브넷에 대한 *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* 권한이 사용자에게 있어야 합니다. 기본 제공 서비스 관리자 역할은 기본적으로 이 권한을 포함합니다. 사용자 지정 역할을 만들어 권한을 수정할 수 있습니다.

기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 사용자 지정 역할에 특정 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. Azure Storage, Azure Key Vault와 같은 특정 Azure 서비스(모두는 아님)는 다양한 AD(Active Directory) 테넌트에서 서비스 엔드포인트도 지원합니다. 즉, 가상 네트워크와 Azure 서비스 리소스는 다른 AD(Active Directory) 테넌트에 있을 수 있습니다. 자세한 내용은 개별 서비스 설명서를 확인하세요.  

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 엔드포인트를 사용하는 추가 비용이 없습니다. Azure 서비스(Azure Storage, Azure SQL Database 등)의 현재 가격 책정 모델은 현재 상태로 적용됩니다.

가상 네트워크에서 서비스 엔드포인트의 총합에 제한이 없습니다.

특정 Azure 서비스(예: Azure Storage 계정)는 리소스 보안에 사용되는 서브넷의 수에 제한을 적용할 수 있습니다. 자세한 내용은 [다음 단계](#next-steps) 섹션에서 다양한 서비스의 설명서를 참조하세요.

## <a name="vnet-service-endpoint-policies"></a>VNet 서비스 엔드포인트 정책 

VNet 서비스 엔드포인트 정책을 사용하면 Azure 서비스에 대한 가상 네트워크 트래픽을 필터링할 수 있습니다. 이 필터는 서비스 엔드포인트에 대한 특정 Azure 서비스 리소스만 허용합니다. 서비스 엔드포인트 정책은 Azure 서비스의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트 정책](./virtual-network-service-endpoint-policies-overview.md)을 참조하세요.

## <a name="faqs"></a>FAQ

FAQ는 [가상 네트워크 서비스 엔드포인트 FAQ](./virtual-networks-faq.md#virtual-network-service-endpoints)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트 구성](tutorial-restrict-network-access-to-resources.md)
- [가상 네트워크에 대한 Azure Storage 계정 보호](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [가상 네트워크에 대한 Azure SQL Database 계정 보호](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [가상 네트워크에 대한 Azure Synapse Analytics 보호](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [프라이빗 엔드포인트와 서비스 엔드포인트 비교](./vnet-integration-for-azure-services.md#compare-private-endpoints-and-service-endpoints)
- [Virtual Network 서비스 엔드포인트 정책](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/vnet-2subnets-service-endpoints-storage-integration)