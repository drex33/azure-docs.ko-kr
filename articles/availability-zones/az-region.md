---
title: 가용성 영역을 지 원하는 Azure 서비스
description: 가용성 영역에서 지원 되는 서비스에 대해 알아보고 모든 Azure 서비스에 대 한 복원 력을 파악 합니다.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: e7c8ce91f15a6b92e3efa9bae8c5a78bc9fd4baa
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129057"
---
# <a name="azure-services-that-support-availability-zones"></a>가용성 영역을 지 원하는 Azure 서비스

Azure 지역 [및 가용성 영역은](az-overview.md) 중복 인프라 및 azure 서비스의 논리적 격리로 인해 데이터 센터 오류에 허용 되는 각 azure 지역 내에서 물리적으로 별도의 위치입니다. 

가용성 영역은 네트워크 중복성에 대 한 것입니다. 가용성 영역을 지 원하는 Azure 서비스는 매우 짧은 대기 시간과 함께 적절 한 수준의 복원 력 및 유연성을 제공 하도록 설계 되었습니다. 가용성 영역을 지 원하는 Azure 서비스를 사용 하 여 사용자 고유의 복원 력을 설계 하는지 아니면 자동 복제 및 배포를 옵트인 하는지 여부에 따라 혜택은 동일 합니다. 서비스 유형과 관계 없이 항상 사용 가능한 서비스에서 뛰어난 복원 력을 얻을 수 있습니다. 

Azure는 모든 서비스 및 제품에 대해 높은 복원 력을 사용 하도록 노력 하 고 있습니다. 가용성 영역을 지 원하는 Azure 서비스를 실행 하면 중단 없이 거의 모든 시나리오에 대해 완전히 투명 하 고 일관 된 복원 력이 제공 됩니다.

## <a name="azure-regions-with-availability-zones"></a>가용성 영역을 사용 하는 Azure 지역

Azure는 클라우드 공급자의 가장 광범위 한 전역 공간을 제공 하 고 새 지역 및 가용성 영역을 신속 하 게 엽니다. 다음 지역은 현재 가용성 영역을 지원 합니다.

| 아메리카 | 유럽 | 아프리카 | 아시아 태평양 |
|--------------------|----------------------|---------------------|----------------|
| 브라질 남부 | 프랑스 중부 | 남아프리카 북부 | 오스트레일리아 동부 |
| 캐나다 중부 | 독일 중서부 | | 인도 중부\* |
| 미국 중부 | 북유럽 | | 일본 동부 |
| 미국 동부 | 노르웨이 동부 | | 한국 중부 |
| 미국 동부 2 | 영국 남부 | | 동남아시아 |
| 미국 중남부 | 서유럽 | | 동아시아* |
| US Gov 버지니아 | 스웨덴 | | |
| 미국 서부 2 | | | |
| 미국 서부 3 | | | |

\* 이 지역에서 가용성 영역 및 사용 가능한 서비스 지원에 대 한 자세한 내용을 보려면 Microsoft 영업 담당자나 고객 담당자에 게 문의 하세요. 가용성 영역을 지 원하는 예정 된 지역에 대 한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조 하세요.

Azure 지역에서 가용성 영역을 지 원하는 Azure 서비스 목록은 [가용성 영역 설명서](az-overview.md)를 참조 하세요.

## <a name="highly-available-services"></a>항상 사용 가능한 서비스

세 가지 유형의 Azure 서비스는 *영역, 영역 중복* 및 *항상 사용 가능한* 서비스의 가용성 영역을 지원 *합니다.* 복원 력 전략을 설계할 때 세 가지 아키텍처 방법을 모두 결합할 수 있습니다.

- 영역 **서비스**: 리소스를 자체 선택 된 특정 가용성 영역에 배포 하 여 보다 엄격한 대기 시간 또는 성능 요구 사항을 달성할 수 있습니다. 복원력은 지역 내 하나 이상의 영역에 애플리케이션 및 데이터를 복제하여 자체 설계됩니다. 리소스는 특정 영역에 고정될 수 있습니다. 예를 들어 가상 머신, 관리 디스크 또는 표준 IP 주소는 특정 영역에 고정 될 수 있으며,이 경우 하나 이상의 리소스 인스턴스를 영역에 분산 하 여 복원 력을 높일 수 있습니다.
- **영역 중복 서비스**: 리소스는 영역 간에 자동으로 복제 되거나 배포 됩니다. 예를 들어 영역 중복 서비스는 한 영역의 오류가 데이터의 고가용성에 영향을 주지 않도록 3 개의 영역으로 데이터를 복제 합니다. 
- **항상 사용 가능한 서비스**: 모든 Azure 지역에서 항상 사용할 수 있으며 영역 전체 중단 및 지역 전체 가동 중단에 대 한 복원 력이 있습니다. Azure에서 비 지역별 서비스 라고도 하는 항상 사용 가능한 서비스의 전체 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.

이 문서 앞부분에서 언급 했 듯이 모든 Azure 서비스는 모든 지역에서 전체적으로 사용할 수 있는 것은 아닙니다. 제품은 _지역_ 가용성을 반영 하는 *기본, 일반* 및 *전략적* 서비스의 세 가지 범주로 그룹화 *됩니다.* 지정 된 지역에 서비스를 배포 하는 방법에 대 한 Azure의 일반 정책은 주로 지역 유형, 서비스 범주 및 고객 수요를 중심으로 합니다. 자세한 내용은 [Azure 서비스](region-types-service-categories-azure.md)를 참조 하세요.

- 기본 **서비스**: 지역이 일반 공급 되는 경우 모든 권장 및 대체 지역에서 사용할 수 있으며, 새 기본 서비스를 일반적으로 사용할 수 있는 90 일 이내에 사용할 수 있습니다.
- **기본 서비스**: 지역의 일반 공급이 90 일 이내에 권장 되는 모든 지역에서 사용할 수 있습니다. 메인스트림 서비스는 대체 지역의 수요를 기반으로 하며, 대부분은 다른 지역의 많은 하위 집합에 이미 배포 되어 있습니다.
- **전략적 서비스**: 대상 서비스 제품으로, 종종 업계에서 집중 하거나 사용자 지정 된 하드웨어에서 지원 됩니다. 전략적 서비스는 지역 간 가용성을 위해 수요를 기반으로 하며, 대부분은 이미 권장 지역의 많은 하위 집합에 배포 됩니다.

영역 및 영역 중복 제공을 포함 하 여 가용성 영역을 지 원하는 Azure 서비스는 지속적으로 확장 됩니다.

이전 세대 가상 컴퓨터에 대 한 자세한 내용은 [이전 세대의 가상 컴퓨터 크기](/azure/virtual-machines/sizes-previous-gen)를 참조 하세요.

다음 표에서는 영역, 영역 중복 및 항상 사용 가능한 Azure 서비스의 현재 제품에 대 한 요약을 제공 합니다. 각각의 지역별 가용성에 따라 Azure 제품을 나열 합니다.

##### <a name="legend"></a>범례
![테이블의 각 서비스에 대 한 서비스 범주 및 지역별 가용성을 고려 하 여 각각의 아이콘과 의미를 포함 하는 범례입니다.](media/legend.png) 

제품 카탈로그에서 항상 사용 가능한 서비스는 "비 지역" 서비스로 나열 됩니다.

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![이 서비스가 기본 서비스 임을 나타내는 아이콘입니다.](media/icon-foundational.svg) 기본 서비스 

| **제품**   | **복원력**   |
| --- | --- |
| [Azure 애플리케이션 게이트웨이 (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| [Azure Backup](/azure/backup/backup-create-rs-vault)  | ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure 공용 IP](../virtual-network/public-ip-addresses.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역 임을 나타내는 아이콘입니다.](media/icon-zonal.svg)  |
| Azure SQL Database ([일반 용도 계층](../azure-sql/database/high-availability-sla.md))  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| Azure SQL Database ([Premium &amp; 중요 비즈니스용 계층](../azure-sql/database/high-availability-sla.md))  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Disk Storage](../storage/common/storage-redundancy.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![이 서비스가 영역 중복 임을 나타내는 아이콘입니다.](media/icon-zone-redundant.svg)  |
| [Azure Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
| [Azure Storage 계정](../storage/common/storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Storage: [핫/쿨 Azure Blob Storage 계층](../storage/common/storage-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| Storage: [Managed Disks](../virtual-machines/managed-disks-overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
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
| Virtual Machines: [F 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [FS 시리즈](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![이 서비스를 나타내는 아이콘은 영역입니다.](media/icon-zonal.svg)  |
| Virtual Machines: [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![이 서비스를 나타내는 아이콘이 일반입니다.](media/icon-mainstream.svg) 일반 서비스

| **제품**   | **복원력**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
| [Azure App Service 환경](../app-service/environment/zone-redundancy.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure API Management](../api-management/zone-redundancy.md)  | ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg)  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
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
| [AKS(Azure Kubernetes Service)](../aks/availability-zones.md)  | ![이 서비스를 나타내는 아이콘은 영역 중복입니다.](media/icon-zone-redundant.svg) ![이 서비스가 영역임을 나타내는 아이콘](media/icon-zonal.svg)  |
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
| Azure Policy  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Resource Graph  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Sentinel  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Stack  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Stack Edge  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Cost Management 및 청구 | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Azure에 대한 고객 Lockbox  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Intune  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Peering Service  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure portal  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Cloud App Security  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Microsoft Graph  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Security Center  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |
| Azure Traffic Manager  | ![이 서비스를 항상 사용할 수 있음을 나타내는 아이콘입니다.](media/icon-always-available.svg) |

Azure 지역별 가용성 영역을 지원하는 Azure 서비스 목록은 가용성 [영역 설명서를 참조하세요.](az-overview.md)

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>가용성 영역의 가상 머신에 대한 가격 책정

Azure 구독을 사용하여 Azure 가용성 영역에 액세스할 수 있습니다. 자세한 내용은 [대역폭 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/bandwidth/)

## <a name="next-steps"></a>다음 단계

- [가용성 영역을 사용하여 고가용성을 위한 솔루션 빌드](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Azure 서비스를 이용한 고가용성](/azure/architecture/framework/resiliency/overview)
- [고가용성을 위한 디자인 패턴](/azure/architecture/framework/resiliency/app-design)
