---
title: 가용성 영역을 지원하는 Azure 서비스
description: Azure에서 가용성과 복원력이 높은 애플리케이션을 만들려면 가용성 영역에서 리소스를 실행하는 데 사용할 수 있는 별도의 실제 위치를 제공합니다.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: prsandhu
ms.reviewer: cnthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: c6620f95c9ec217771cd14c756e60fd0eb6d90c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659283"
---
# <a name="azure-services-that-support-availability-zones"></a>가용성 영역을 지원하는 Azure 서비스

Microsoft Azure 글로벌 인프라는 고객에게 가장 높은 수준의 중복성 및 복원력을 제공하기 위해 모든 계층에서 설계되고 생성됩니다. Azure 인프라는 지역 및 가용성 영역으로 구성되어 있으며, 이를 통해 실패의 블래스트 반경(blast radius)을 제한하여 고객 애플리케이션 및 데이터에 미치는 잠재적인 영향을 제한합니다. Azure 가용성 영역 구문은 데이터 센터 오류로부터 보호하는 소프트웨어 및 네트워킹 솔루션을 제공하고 증가된 HA(고가용성)를 고객에게 제공하기 위해 개발되었습니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹이 있는 하나 이상의 데이터 센터로 구성됩니다. 지역 내에서 가용성 영역을 물리적으로 분리하면 대규모 시간 초과, 주요 스톰 및 슈퍼스톰, 사이트 액세스, 안전 통로, 확장 유틸리티 작동 시간 및 리소스 가용성을 방해할 수 있는 기타 이벤트와 같은 영역 장애가 애플리케이션 및 데이터에 미치는 영향을 제한할 수 있습니다. 가용성 영역 및 그와 연결된 데이터 센터는 한 영역이 손상되면 해당 지역의 다른 가용성 영역에서 서비스, 용량 및 가용성을 지원하도록 설계되었습니다.

모든 Azure 관리 서비스는 지역 수준의 오류에 탄력적으로 대처할 수 있도록 설계되었습니다. 오류가 발생하는 경우, 지역 내 하나 이상의 가용성 영역 오류는 전체 지역 오류와 비교하여 작은 오류입니다. Azure는 지역 내에서 관리 서비스의 영역 수준 오류로부터 복구할 수 있습니다. Azure는 지역 내에서 한 번에 한 영역마다 중요한 유지 관리를 수행하여 지역 내의 가용성 영역에 배포된 고객 리소스에 영향을 주는 오류를 방지합니다.


![3개의 영역이 있는 Azure 지역의 개념 보기](./media/az-region/azure-region-availability-zones.png)


가용성 영역을 지원하는 Azure 서비스는 **영역**, **영역 중복** 및 **비지역** 서비스의 세 가지 범주로 나뉩니다. 고객 작업은 범주별로 분류하여 이러한 애플리케이션 성능 및 내구성에 맞게 아키텍처 시나리오를 활용할 수 있습니다.

- **영역 서비스** – 리소스를 자체 선택된 특정 가용성 영역에 배포하여 보다 엄격한 대기 시간 또는 성능 요구 사항을 달성할 수 있습니다.  복원력은 지역 내 하나 이상의 영역에 애플리케이션 및 데이터를 복제하여 자체 설계됩니다.  리소스는 특정 영역에 고정될 수 있습니다. 예를 들어 가상 머신, 관리 디스크 또는 표준 IP 주소는 특정 영역에 고정될 수 있으며, 이 경우 하나 이상의 리소스 인스턴스를 영역에 분산하여 복원력을 높일 수 있습니다.

- **영역 중복 서비스** – 리소스가 영역 간에 자동으로 복제되거나 분산됩니다. 예를 들어 ZRS는 영역 오류가 데이터의 HA에 영향을 주지 않도록 세 개의 영역에 데이터를 복제합니다.  

- **비지역 서비스** – 이 서비스는 항상 Azure 지역에서 사용할 수 있으며 영역 전체 중단뿐만 아니라 지역 전체 중단도 복원할 수 있습니다. 


Azure에서 포괄적인 비즈니스 연속성을 구현하려면 Azure 지역 쌍과 가용성 영역의 조합을 사용하여 애플리케이션 아키텍처를 빌드하십시오. 고가용성에 대한 Azure 지역 내의 가용성 영역을 사용하여 애플리케이션 및 데이터를 동기적으로 복제하고 재해 복구 보호에 대한 Azure 지역에서 비동기적으로 복제할 수 있습니다. 자세히 알아보려면 [가용성 영역을 사용하여 고가용성을 위한 솔루션 빌드](/azure/architecture/high-availability/building-solutions-for-high-availability)를 참조하세요. 

## <a name="azure-services-supporting-availability-zones"></a>가용성 영역을 지원하는 Azure 서비스

 - 이전 세대의 가상 머신은 나열되지 않습니다. 자세한 내용은 [이전 세대의 가상 머신 크기](../virtual-machines/sizes-previous-gen.md)를 참조하세요.
 - [Azure의 지역 및 가용성 영역](az-overview.md)에 설명된 대로 일부 서비스는 비지역 서비스입니다. 이러한 서비스는 특정 Azure 지역에 대한 종속성이 없으므로 영역 전체 중단 및 지역 전체 중단에 탄력적으로 대처할 수 있습니다.  비지역 서비스 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 찾을 수 있습니다.


## <a name="azure-regions-with-availability-zones"></a>가용성 영역을 포함하는 Azure 지역
 

| 아메리카           | 유럽               | 아프리카              | 아시아 태평양   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| 브라질 남부       | 프랑스 중부       | 남아프리카 북부* | 오스트레일리아 동부 |
| 캐나다 중부     | 독일 중서부 |                     | 인도 중부* |
| 미국 중부         | 북유럽         |                     | 일본 동부     |
| 미국 동부            | 노르웨이 동부          |                     | 한국 중부* |
| 미국 동부 2          | 영국 남부             |                     | 동남아시아 |
| 미국 중남부 | 서유럽          |                     |                |
| US Gov 버지니아    |                      |                     |                |
| 미국 서부 2        |                      |                     |                |
| 미국 서부 3        |                      |                     |                |

\* 이 지역에서 가용성 영역 및 사용 가능한 서비스 지원에 대한 자세한 내용은 Microsoft 영업 담당자나 고객 담당자에게 문의하세요. 가용성 영역을 지원할 예정된 지역에 대해서는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조하세요.


## <a name="azure-services-supporting-availability-zones"></a>가용성 영역을 지원하는 Azure 서비스

- 아래에 이전 세대 가상 머신은 나열되어 있지 않습니다. 자세한 내용은 [이전 세대의 가상 머신 크기](../virtual-machines/sizes-previous-gen.md)를 참조하세요.

- 일부 서비스는 비지역 서비스입니다. 자세한 내용은 [Azure의 지역 및 가용성 영역](az-overview.md)을 참조하세요. 이러한 서비스는 특정 Azure 지역에 대한 종속성이 없으므로 영역 전체 중단 및 지역 전체 중단에 탄력적으로 대처할 수 있습니다.  비지역 서비스 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 찾을 수 있습니다.


### <a name="zone-resilient-services"></a>영역 복원력 서비스 

:globe_with_meridians: 비지역 서비스 - 이 서비스는 항상 Azure 지역에서 사용할 수 있으며 영역 전체 중단뿐만 아니라 지역 전체 중단도 복원할 수 있습니다.

:large_blue_diamond:   영역 전체의 중단에 대한 복원 

**기본 서비스**

|     제품                                                    | 복원력             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application   Gateway(V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)                                  | :large_blue_diamond:  |
|     [Azure Backup](../backup/backup-create-rs-vault.md#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos   DB](../cosmos-db/high-availability.md#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data   Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)                             | :large_blue_diamond:  |
|     [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)                   | :large_blue_diamond:  |
|     [Azure Express   Route](../expressroute/designing-for-high-availability-with-expressroute.md)                                       | :large_blue_diamond:  |
|     [Azure 공용   IP](../virtual-network/public-ip-addresses.md)                                           | :large_blue_diamond:  |
|     Azure SQL   Database([범용 계층](../azure-sql/database/high-availability-sla.md))                 | :large_blue_diamond:  |
|     Azure SQL   Database([프리미엄 & 중요 비즈니스용 계층](../azure-sql/database/high-availability-sla.md))     | :large_blue_diamond:  |
|     [Disk Storage](../storage/common/storage-redundancy.md)                                                | :large_blue_diamond:  |
|     [Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)                                                  | :large_blue_diamond:  |
|     [Key Vault](../key-vault/general/disaster-recovery-guidance.md)                                                   | :large_blue_diamond:  |
|     [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md)                                               | :large_blue_diamond:  |
|     [Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service   Fabric](../service-fabric/service-fabric-cross-availability-zones.md)                                            | :large_blue_diamond:  |
|     [스토리지   계정](../storage/common/storage-redundancy.md)                                           | :large_blue_diamond:  |
|     Storage:   [핫/쿨 Blob Storage 계층](../storage/common/storage-redundancy.md)                      | :large_blue_diamond:  |
|     Storage:   [Managed Disks](../virtual-machines/managed-disks-overview.md)                                    | :large_blue_diamond:  |
|     [Virtual   Machines Scale Sets](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)                               | :large_blue_diamond:  |
|     [Virtual   Machines](../virtual-machines/windows/create-powershell-availability-zone.md)                                          | :large_blue_diamond:  |
|     Virtual   Machines: [Av2-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual   Machines: [Bs-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual   Machines: [DSv2-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual   Machines: [DSv3-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                            | :large_blue_diamond:  |
|     Virtual   Machines: [Dv2-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual   Machines: [Dv3-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual   Machines: [ESv3-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual   Machines: [Ev3-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual   Machines: [F-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual   Machines: [FS-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual   Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)                                         | :large_blue_diamond:  |
|     [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                                             | :large_blue_diamond:  |


**일반 서비스**


|     제품                                                    | 복원력             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service](../app-service/how-to-zone-redundancy.md)                                    | :large_blue_diamond:  |
|     [App Service Environment](../app-service/environment/zone-redundancy.md)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md)                      | :large_blue_diamond:  |
|     [Azure API Management](../api-management/zone-redundancy.md)                      | :large_blue_diamond:  |
|     [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)   | :large_blue_diamond:  |    
|     [Azure Batch](/azure/batch/create-pool-availability-zones)                                               | :large_blue_diamond:  |
|     [Azure Bastion](../bastion/bastion-overview.md)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Text Analytics](../cognitive-services/text-analytics/index.yml)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     [Azure Data Factory](../data-factory/index.yml)                               | :large_blue_diamond:  |
|     Azure Database for MySQL - [유연한 서버](../mysql/flexible-server/concepts-high-availability.md)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL – [유연한 서버](../postgresql/flexible-server/overview.md)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)                                       | :large_blue_diamond:  |
|     [Azure 디스크 암호화](../virtual-machines/disks-redundancy.md)                                       | :large_blue_diamond:  |
|     [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)                                      | :large_blue_diamond:  |
|     [Azure Functions](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)     | :large_blue_diamond:  |
|     [AKS(Azure Kubernetes Service)](../aks/availability-zones.md)                              | :large_blue_diamond:  |
|     [AMS(Azure Media Services)](../media-services/latest/concept-availability-zones.md)        | :large_blue_diamond:  |
|     [Azure Monitor](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Monitor: Application Insights](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Monitor: Log Analytics](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Private Link](../private-link/private-link-overview.md)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)                                       | :large_blue_diamond:  |
|     Azure SQL: [가상 머신](../azure-sql/database/high-availability-sla.md)                                                                 | :large_blue_diamond:  |
|     [Azure 웹 애플리케이션 방화벽](../firewall/deploy-availability-zone-powershell.md)                                                         | :large_blue_diamond:  |
|     [Container Registry](../container-registry/zone-redundancy.md)                                                                               | :large_blue_diamond:  |
|     [Event Grid](../event-grid/overview.md)                                                                                                      | :large_blue_diamond:  |
|     [HDInsight](/azure/hdinsight/hdinsight-use-availability-zones)                                                                               | :large_blue_diamond:  |
|     [Network Watcher](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                     | :large_blue_diamond:  |
|     Network Watcher: [트래픽 분석](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                  | :large_blue_diamond:  |
|     [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)                                                      | :large_blue_diamond:  |
|     [Premium Blob Storage](../storage/blobs/storage-blob-performance-tiers.md)                                        | :large_blue_diamond:  |
|     Storage: [Azure Premium Files](../storage/files/storage-files-planning.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [M-Series](../virtual-machines/windows/create-powershell-availability-zone.md)                                  | :large_blue_diamond:  |
|     [가상 WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [지점 및 사이트 간 VPN 게이트웨이](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                      | :large_blue_diamond:  |
|     Virtual WAN: [사이트 간 VPN 게이트웨이](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                       | :large_blue_diamond:  |


**특수 서비스**

|     제품                                                    | 복원력             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: Anomaly Detector                        | :large_blue_diamond:  |
|     Cognitive Services: Form Recognizer                         | :large_blue_diamond:  |
|     Storage: Ultra Disk                                         | :large_blue_diamond:  |


**비지역**

|     제품                                                    | 복원력             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active   Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Service                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure   Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure   Information Protection                              | :globe_with_meridians: |
|     Azure   Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed   Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure   Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack   Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Customer Lockbox   for Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft   Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft   Azure Portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security   Center                                           | :globe_with_meridians: |
|     Traffic   Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>가용성 영역의 VM에 대한 가격 책정

Azure 가용성 영역은 Azure 구독과 함께 사용할 수 있습니다. 자세히 알아보기 - [대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>가용성 영역 시작하기

- [가상 머신 만들기](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell을 사용하여 Managed Disk 추가](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [영역 중복 가상 머신 확장 집합 만들기](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역에서 VM 부하 분산](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역 내에 VM 부하 분산](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [영역 중복 스토리지](../storage/common/storage-redundancy.md)
- [SQL Database 범용 계층](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus 지리적 재해 복구](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [영역 중복 가상 네트워크 게이트웨이 만들기](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB에 대한 영역 중복 지역 추가](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure Cache for Redis 가용성 영역 시작](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services 인스턴스 만들기](../active-directory-domain-services/tutorial-create-instance.md)
- [가용성 영역을 사용하는 AKS(Azure Kubernetes Service) 클러스터 만들기](../aks/availability-zones.md)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 지역 및 가용성 영역](az-overview.md)
