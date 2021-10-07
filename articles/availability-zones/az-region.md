---
title: 가용성 영역을 지원하는 Azure 서비스
description: 가용성 영역에서 지원되는 서비스를 알아보고 모든 Azure 서비스의 복원력을 이해합니다.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: e2c60b8780709eb1eb6890a81dde16b97df8219a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612645"
---
# <a name="azure-services-that-support-availability-zones"></a>가용성 영역을 지원하는 Azure 서비스

Azure [지역 및 가용성 영역은](az-overview.md) 중복 인프라 및 Azure 서비스의 논리적 격리로 인해 데이터 센터 오류에 내포되는 각 Azure 지역 내에서 물리적으로 분리된 위치입니다. 

가용성 영역은 모두 네트워크 중복성에 관한 것입니다. 가용성 영역을 지원하는 Azure 서비스는 매우 짧은 대기 시간과 함께 적절한 수준의 복원력과 유연성을 제공하도록 설계되었습니다. 가용성 영역을 지원하는 Azure 서비스에서는 고유한 복원력을 설계하든 자동 복제 및 배포를 선택하든 관계없이 동일한 이점이 있습니다. 서비스 유형에 관계없이 고가용성 서비스에서 뛰어난 복원력을 얻을 수 있습니다. 

Azure는 모든 서비스 및 제품에서 높은 복원력을 제공하기 위해 노력하고 있습니다. 가용성 영역을 지원하는 Azure 서비스를 실행하면 중단 없이 거의 모든 시나리오에 대해 완전히 투명하고 일관된 복원력을 제공합니다.

## <a name="azure-regions-with-availability-zones"></a>가용성 영역이 있는 Azure 지역

Azure는 모든 클라우드 공급자의 가장 광범위한 글로벌 공간을 제공하며 새로운 지역 및 가용성 영역을 빠르게 열고 있습니다. 다음 지역은 현재 가용성 영역을 지원합니다.

| 아메리카 | 유럽 | 아프리카 | 아시아 태평양 |
|--------------------|----------------------|---------------------|----------------|
| 브라질 남부 | 프랑스 중부 | 남아프리카 북부\* | 오스트레일리아 동부 |
| 캐나다 중부 | 독일 중서부 | | 인도 중부\* |
| 미국 중부 | 북유럽 | | 일본 동부 |
| 미국 동부 | 노르웨이 동부 | | 한국 중부 |
| 미국 동부 2 | 영국 남부 | | 동남아시아 |
| 미국 중남부 | 서유럽 | | |
| US Gov 버지니아 | 스웨덴* | | |
| 미국 서부 2 | | | |
| 미국 서부 3 | | | |

\* 이 지역의 가용성 영역 및 사용 가능한 서비스 지원에 대해 자세히 알아보려면 Microsoft 영업 또는 고객 담당자에게 문의하세요. 가용성 영역을 지원할 향후 지역에 대한 자세한 내용은 [Azure 지역을 참조하세요.](https://azure.microsoft.com/global-infrastructure/geographies/)

Azure 지역별 가용성 영역을 지원하는 Azure 서비스 목록은 가용성 [영역 설명서를 참조하세요.](az-overview.md)

## <a name="highly-available-services"></a>고가용성 서비스

세 가지 유형의 Azure 서비스는 가용성 영역을 지원합니다. *영역,* *영역 중복* 및 *항상 사용 가능한* 서비스. 복원력 전략을 설계할 때 이러한 세 가지 아키텍처 접근 방식을 모두 결합할 수 있습니다.

- **영역 서비스:** 리소스를 자체 선택된 특정 가용성 영역에 배포하여 보다 엄격한 대기 시간 또는 성능 요구 사항을 달성할 수 있습니다. 복원력은 지역 내 하나 이상의 영역에 애플리케이션 및 데이터를 복제하여 자체 설계됩니다. 리소스는 특정 영역에 고정될 수 있습니다. 예를 들어 가상 머신, 관리 디스크 또는 표준 IP 주소를 특정 영역에 고정할 수 있습니다. 이렇게 하면 영역 간에 하나 이상의 리소스 인스턴스를 분산하여 복원력을 높일 수 있습니다.
- **영역 중복 서비스:** 리소스가 영역 간에 자동으로 복제되거나 분산됩니다. 예를 들어 영역 중복 서비스는 한 영역의 오류가 데이터의 고가용성 영향을 받지 않도록 세 영역에 걸쳐 데이터를 복제합니다. 
- **항상 사용 가능한 서비스:** 모든 Azure 지역에서 항상 사용할 수 있으며 영역 전체 중단 및 지역 전체 중단에 탄력적입니다. Azure에서 지역 이외의 서비스라고도 하는 항상 사용 가능한 서비스의 전체 목록은 [지역별 사용 가능한 제품을 참조하세요.](https://azure.microsoft.com/global-infrastructure/services/)

이 문서의 앞에서 설명한 것처럼 모든 Azure 서비스가 모든 지역에서 범용으로 제공되는 것은 아닙니다. 제품은 _지역_ 가용성을 반영하는 기본 *,* *일반* 및 *전략적* 서비스의 세 가지 범주로 그룹화됩니다. 지정된 지역에 서비스를 배포하는 Azure의 일반 정책은 주로 지역 유형, 서비스 범주 및 고객 수요에 따라 좌우됩니다. 자세한 내용은 [Azure 서비스를 참조하세요.](region-types-service-categories-azure.md)

- **기본 서비스:** 지역을 일반적으로 사용할 수 있는 경우 또는 새 기본 서비스가 일반 제공되고 90일 이내에 모든 권장 및 대체 지역에서 사용할 수 있습니다.
- **일반 서비스:** 지역의 일반 공급 후 90일 이내에 모든 권장 지역에서 사용할 수 있습니다. 일반 서비스는 대체 지역에서 수요 기반이며, 많은 서비스가 대체 지역의 대규모 하위 집합에 이미 배포되어 있습니다.
- **전략적 서비스:** 대상 서비스 제품으로, 업계 중심이거나 사용자 지정된 하드웨어에서 지원되는 경우가 많습니다. 전략적 서비스는 지역 간 가용성을 위해 수요 기반이며, 많은 서비스가 이미 권장되는 지역의 대규모 하위 집합에 배포되어 있습니다.

영역 및 영역 중복 제품을 포함하여 가용성 영역을 지원하는 Azure 서비스는 지속적으로 확장되고 있습니다.

이전 세대 가상 머신에 대한 자세한 내용은 [이전 세대의 가상 머신 크기 를 참조하세요.](/azure/virtual-machines/sizes-previous-gen)

다음 표에서는 영역, 영역 중복 및 항상 사용 가능한 Azure 서비스의 현재 제공 사항에 대한 요약을 제공합니다. 각 제품의 지역별 가용성에 따라 Azure 제품을 나열합니다.

##### <a name="legend"></a>범례
![표에 있는 각 서비스의 서비스 범주 및 지역별 가용성과 관련하여 각 서비스의 아이콘과 의미를 포함하는 범례입니다.](media/legend.png) 

제품 카탈로그에서 항상 사용 가능한 서비스는 "비 지역" 서비스로 나열됩니다.

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![이 서비스를 나타내는 아이콘은 기본입니다.](media/icon-foundational.svg) 기본 서비스 

| **제품**   | **복원력**   |
| --- | --- |
| [Azure Application Gateway(V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Backup](/architecture/backup/backup-create-rs-vault#set-storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure 공용 IP](../virtual-network/public-ip-addresses.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Azure SQL Database( 범용[계층](../azure-sql/database/high-availability-sla.md))  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Azure SQL Database([Premium 중요 비즈니스용 &amp; 계층](../azure-sql/database/high-availability-sla.md))  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Disk Storage](../storage/common/storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Storage 계정](../storage/common/storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Storage: [핫/쿨 Azure Blob Storage 계층](../storage/common/storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Storage: [Managed Disks](../virtual-machines/managed-disks-overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Virtual Machines](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Av2 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Bs 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [DSv2 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [DSv3 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv2 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv3 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [ESv3 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Ev3 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: F [시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [FS 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![이 서비스를 나타내는 아이콘이 일반입니다.](media/icon-mainstream.svg) 일반 서비스

| **제품**   | **복원력**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure App Service 환경](../app-service/environment/zone-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure API Management](../api-management/zone-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: [Text Analytics](../cognitive-services/text-analytics/index.yml)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Data Factory](../data-factory/index.yml)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Azure Database for MySQL – [유연한 서버](../mysql/flexible-server/concepts-high-availability.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Azure Database for PostgreSQL – [유연한 서버](../postgresql/flexible-server/overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) |
| [Azure 디스크 암호화](../virtual-machines/disks-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Functions](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [AKS(Azure Kubernetes Service)](../aks/availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Application Insights](../azure-monitor/logs/availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Private Link](../private-link/private-link-overview.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Azure SQL: [가상 컴퓨터](../azure-sql/database/high-availability-sla.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg) |
| [Azure 웹 애플리케이션 방화벽](../firewall/deploy-availability-zone-powershell.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Container Registry](../container-registry/zone-redundancy.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Event Grid](../event-grid/overview.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| [Azure Network Watcher](../network-watcher/frequently-asked-questions.yml)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Network Watcher: [트래픽 분석](../network-watcher/frequently-asked-questions.yml)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Premium Blob Storage](../storage/blobs/storage-blob-performance-tiers.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Storage: [Azure Premium 파일](../storage/files/storage-files-planning.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Virtual Machines: [Azure 전용 호스트](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Ddsv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Ddv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Dsv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Edsv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Edv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Esv4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Ev4 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Fsv2 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [M 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| [Azure 가상 WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| 가상 WAN: [Azure express](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan) 경로  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| 가상 WAN: [지점 및 사이트 간 VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| 가상 WAN: [사이트 간 VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![이 서비스가 전략적 임을 나타내는 아이콘입니다.](media/icon-strategic.svg) 전략적 서비스

| **제품**   | **복원력**   |
| --- | --- |
| Azure Red Hat OpenShift  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Azure Cognitive Services: Anomaly Detector  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: Form Recognizer  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Azure Storage: Ultra Disk  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![이 서비스가 비 지역 임을 나타내는 아이콘입니다.](media/icon-always-available.svg) 비 지역 서비스 (항상 사용 가능한 서비스)

| **제품**   | **복원력**   |
| --- | --- |
| Azure DNS  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Active Directory  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Advanced Threat Protection  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Advisor  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Blueprints  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Bot Service  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Front Door  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| IoT용 Azure Defender  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Front Door  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Information Protection  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Lighthouse  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure 관리되는 애플리케이션  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Maps  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Policy  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Resource Graph  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Sentinel  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Stack  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Stack Edge  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Cost Management 및 청구 | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Azure에 대한 고객 Lockbox  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Intune  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Peering Service  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure portal  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Cloud App Security  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Graph  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Security Center  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Traffic Manager  | ![이 서비스는 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |

Azure 지역에서 가용성 영역을 지 원하는 Azure 서비스 목록은 [가용성 영역 설명서](az-overview.md)를 참조 하세요.

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>가용성 영역에 있는 가상 머신의 가격

Azure 구독을 사용 하 여 Azure 가용성 영역에 액세스할 수 있습니다. 자세히 알아보려면 [대역폭 가격](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [가용성 영역을 사용 하 여 고가용성을 위한 솔루션 빌드](/architecture/high-availability/building-solutions-for-high-availability)
- [Azure 서비스를 통한 고가용성](/architecture/framework/resiliency/overview)
- [고가용성을 위한 디자인 패턴](/architecture/framework/resiliency/app-design)
