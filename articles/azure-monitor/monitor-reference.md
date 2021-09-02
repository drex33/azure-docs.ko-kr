---
title: Azure Monitor에서 모니터링하는 항목
description: Azure Monitor에서 모니터링하는 모든 서비스 및 기타 리소스에 대한 참조입니다.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 1e43944b803f798c3bd741903f1ee0732bcb93d4
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864536"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor에서 모니터링하는 항목
이 문서에서는 Azure Monitor에서 모니터링하는 다양한 애플리케이션 및 서비스에 대해 설명합니다. 

## <a name="insights-and-core-solutions"></a>인사이트 및 핵심 솔루션
핵심 인사이트 및 솔루션은 Azure Monitor의 일부로 간주되며 Azure에 대한 지원 및 서비스 수준 계약을 따릅니다. Azure Monitor를 사용할 수 있는 모든 Azure 지역에서 지원됩니다.

### <a name="insights"></a>자세한 정보

인사이트는 특정 애플리케이션 및 서비스에 대한 사용자 지정 모니터링 환경을 제공합니다. 로그 및 메트릭을 모두 수집하고 분석합니다.

| 인사이트 | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 모든 플랫폼에서 라이브 웹 애플리케이션을 모니터링하는 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. |
| [컨테이너 인사이트](containers/container-insights-overview.md) | AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. |
| [Cosmos DB 인사이트](insights/cosmosdb-insights-overview.md) | 통합 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전체 성능, 실패, 용량 및 운영 상태를 보여 줍니다. |
| [네트워크 인사이트(미리 보기)](insights/network-insights-overview.md) | 모든 네트워크 리소스에 대한 상태 및 메트릭의 포괄적인 보기를 제공합니다. 고급 검색 기능은 리소스 종속성을 식별하는 데 유용하여 웹 사이트 이름을 검색하기만 하면 웹 사이트를 호스트하는 리소스를 식별하는 등의 시나리오를 사용할 수 있습니다. |
[리소스 그룹 인사이트(미리 보기)](insights/resource-group-insights.md) |  리소스 그룹의 상태 및 성능과 관련된 컨텍스트를 전체적으로 제공하면서 개별 리소스에 발생하는 문제를 심사하고 진단할 수 있습니다. |
| [Storage 인사이트](../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합 보기를 제공하여 Azure Storage 계정의 포괄적인 모니터링을 제공합니다. |
| [VM 인사이트](vm/vminsights-overview.md) | Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. |
| [Key Vault 인사이트(미리 보기)](./insights/key-vault-insights-overview.md) | Key Vault 요청, 성능, 실패 및 대기 시간 통합 보기를 제공하여 키 자격 증명 모음에 대한 종합적인 모니터링을 제공합니다. |
| [Azure Cache for Redis insights(미리 보기)](insights/redis-cache-insights-overview.md) |  전반적인 성능, 실패, 용량 및 운영 상태에 대한 통합된 대화형 보기를 제공합니다. |


### <a name="core-solutions"></a>핵심 솔루션

솔루션은 특정 애플리케이션이나 서비스에 대해 사용자 지정된 로그 쿼리 및 보기를 기반으로 합니다. 로그만 수집 및 분석하며, 인사이트를 위해 시간이 지남에 따라 더 이상 사용되지 않습니다.

| 해결 방법 | Description |
|:---|:---|
| [에이전트 상태](insights/solution-agenthealth.md) | Log Analytics 에이전트의 상태와 구성을 분석합니다. |
| [경고 관리](insights/alert-management-solution.md) | System Center Operations Manager, Nagios 또는 Zabbix에서 수집된 경고를 분석합니다. |
| [서비스 맵](vm/service-map.md) | Windows 및 Linux 시스템에서 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. |



## <a name="azure-services"></a>Azure 서비스
다음 표에는 Azure 서비스와 Azure Monitor에 수집되는 데이터가 나열되어 있습니다. 

- 메트릭 - 서비스는 메트릭을 Azure Monitor 메트릭에 자동으로 수집합니다. 
- 로그 - 서비스는 Azure Monitor 로그에 대한 플랫폼 로그 및 메트릭을 수집할 수 있는 진단 설정을 지원합니다
- 인사이트 - 서비스에 대한 사용자 지정된 모니터링 환경을 제공하는 서비스에 대한 인사이트입니다.

| 서비스 | 메트릭 | 로그 | 인사이트 | 메모 |
|:---|:---|:---|:---|:---|
|Active Directory | 예 | 예 | [예](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 예 | 아니요 | 아니요 |  |
|Active Directory Domain Services | 예 | 예 | 예 |  |
|활동 로그 | 예 | 예 | 예 | |
|고급 위협 보호 | 예 | 아니요 | 예 |  |
|Advisor | 예 | 아니요 | 예 |  |
|AI Builder | 예 | 아니요 | 예 |  |
|Analysis Services | 예 | 예 | 예 |  |
|FHIR용 API | 예 | 아니요 | 예 |  |
|API Management | 예 | 예 | 예 |  |
|App Service | 예 | 예 | 예 |  |
|AppConfig | 예 | 아니요 | 예 |  |
|Application Gateway | 예 | 예 | 예 |  |
|증명 상태 | 예 | 아니요 | 예 |  |
|Automation | 예 | 예 | 예 |  |
|Azure Service Manager(RDFE) | 예 | 아니요 | 예 |  |
|Backup | 예 | 예 | 예 |  |
|Bastion | 예 | 아니요 | 예 |  |
|Batch | 예 | 예 | 예 |  |
|Batch AI | 예 | 아니요 | 예 |  |
|Blockchain Service | 예 | 예 | 예 |  |
|Blueprints | 예 | 아니요 | 예 |  |
|Bot 서비스 | 예 | 아니요 | 예 |  |
|Cloud Services | 예 | 예 | 예 | 게스트 운영 체제 및 워크플로를 모니터링하는 데 에이전트가 필요합니다.  |
|Cloud Shell | 예 | 아니요 | 예 |  |
|Cognitive Services | 예 | 예 | 예 |  |
|Container Instances | 예 | 아니요 | 예 |  |
|Container Registry | 예 | 예 | 예 |  |
|CDN(콘텐츠 전송 네트워크) | 예 | 예 | 예 |  |
|Cosmos DB | 예 | 예 | [예](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | 예 | 아니요 | 예 |  |
|Data Box | 예 | 아니요 | 예 |  |
|Data Catalog Gen2 | 예 | 아니요 | 예 |  |
|Data Explorer | 예 | 예 | 예 |  |
|Data Factory | 예 | 예 | 예 |  |
|Data Factory v2 | 예 | 예 | 예 |  |
|Data Share | 예 | 아니요 | 예 |  |
|Database for MariaDB | 예 | 예 | 예 |  |
|Database for MySQL | 예 | 예 | 예 |  |
|Database for PostgreSQL | 예 | 예 | 예 |  |
|Database Migration Service | 예 | 아니요 | 예 |  |
|Databricks | 예 | 예 | 예 |  |
|DDoS Protection | 예 | 예 | 예 |  |
|DevOps | 예 | 아니요 | 예 |  |
|DNS | 예 | 아니요 | 예 |  |
|도메인 이름 | 예 | 아니요 | 예 |  |
|DPS | 예 | 아니요 | 예 |  |
|Dynamics 365 Customer Engagement | 예 | 아니요 | 예 |  |
|Dynamics 365 Finance and Operations | 예 | 아니요 | 예 |  |
|Event Grid | 예 | 아니요 | 예 |  |
|Event Hubs | 예 | 예 | 예 |  |
|ExpressRoute | 예 | 예 | 예 |  |
|방화벽 | 예 | 예 | 예 |  |
|Front Door | 예 | 예 | 예 |  |
|Functions | 예 | 예 | 예 |  |
|HDInsight | 예 | 예 | 예 |  |
|HPC Cache | 예 | 아니요 | 예 |  |
|Information Protection | 예 | 예 | 예 |  |
|Intune | 예 | 예 | 예 |  |
|IoT Central | 예 | 아니요 | 예 |  |
|IoT Hub | 예 | 예 | 예 |  |
|Key Vault | 예 | 예 | [예](./insights/key-vault-insights-overview.md) |  |
|AKS(Azure Kubernetes Service) | 예 | 아니요 | [예](containers/container-insights-overview.md)  |  |
|Load Balancer | 예 | 아니요 | 예 |  |
|Logic Apps | 예 | 예 | 예 |  |
|Machine Learning Service | 예 | 아니요 | 예 |  |
|Managed Applications  | 예 | 아니요 | 예 |  |
|지도  | 예 | 아니요 | 예 |  |
|Media Services | 예 | 예 | 예 |  |
|Microsoft Managed Desktop | 예 | 아니요 | 아니요 |  |
|Microsoft Power Apps | 아니요 | 아니요 | 예 |  |
|Microsoft 소셜 참여 | 예 | 아니요 | 예 |  |
|Microsoft Stream | 예 | 예 | 예 |  |
|마이그레이션 | 예 | 아니요 | 예 |  |
|Multi-Factor Authentication | 예 | 예 | 예 |  |
|Network Watcher | 예 | 예 | 예 |  |
|Notification Hubs | 예 | 아니요 | 예 |  |
|공개 데이터 세트 | 예 | 아니요 | 예 |  |
|정책 | 예 | 아니요 | 예 |  |
|Power Automate | 예 | 아니요 | 아니요 |  |
|Power BI Embedded | 예 | 예 | 예 |  |
|Private Link | 예 | 아니요 | 예 |  |
|프로젝트 Spool 통신 플랫폼 | 예 | 아니요 | 예 |  |
|Red Hat OpenShift | 예 | 아니요 | 예 |  |
|Redis Cache | 예 | 예 | [예](insights/redis-cache-insights-overview.md) | |
|Resource Graph | 예 | 아니요 | 예 |  |
|리소스 관리자 | 예 | 아니요 | 예 |  |
|소매점 검색 – Bing에서 | 예 | 아니요 | 예 |  |
|검색 | 예 | 예 | 예 |  |
|Service Bus | 예 | 예 | 예 |  |
|Service Fabric | 예 | 예 | 예 | 게스트 운영 체제 및 워크플로를 모니터링하는 데 에이전트가 필요합니다.  |
|등록 포털 | 예 | 아니요 | 예 |  |
|Site Recovery | 예 | 예 | 예 |  |
|Spring Cloud Service | 예 | 아니요 | 예 |  |
|Azure Synapse Analytics | 예 | 예 | 예 |  |
|SQL Database | 예 | 예 | 예 |  |
|SQL Server Stretch Database | 예 | 예 | 예 |  |
|스택 | 예 | 아니요 | 예 |  |
|스토리지 | 예 | 아니요 | [예](../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) |  |
|스토리지 캐시 | 예 | 아니요 | 예 |  |
|스토리지 동기화 서비스 | 예 | 아니요 | 예 |  |
|Stream Analytics | 예 | 예 | 예 |  |
|Time Series Insights | 예 | 예 | 예 |  |
|TINA | 예 | 아니요 | 예 |  |
|Traffic Manager | 예 | 예 | 예 |  |
|유니버설 인쇄 | 예 | 아니요 | 예 |  |
|Virtual Machine Scale Sets | 예 | 예 | [예](vm/vminsights-overview.md) | 게스트 운영 체제 및 워크플로를 모니터링하는 데 에이전트가 필요합니다. |
|Virtual Machines | 예 | 예 | [예](vm/vminsights-overview.md) | 게스트 운영 체제 및 워크플로를 모니터링하는 데 에이전트가 필요합니다. |
|Virtual Network | 예 | 예 | [예](insights/network-insights-overview.md) |  |
|Virtual Network - NSG 흐름 로그 | 예 | 예 | 예 |  |
|VPN Gateway | 예 | 예 | 예 |  |
|Windows Virtual Desktop | 예 | 예 | 예 |  |

## <a name="virtual-machine-agents"></a>가상 머신 에이전트
다음 표에는 가상 컴퓨터의 게스트 운영 체제에서 데이터를 수집하고 모니터에 데이터를 보낼 수 있는 에이전트가 나열되어 있습니다. 각 에이전트는 서로 다른 데이터를 수집하여 Azure Monitor의 메트릭 또는 로그에 보낼 수 있습니다. 

각 에이전트가 수집할 수 있는 데이터에 대한 자세한 내용은 [Azure Monitor 에이전트 개요](agents/agents-overview.md)를 참조하세요.

| 에이전트 |  메트릭 | 로그 |
|:---|:---|:---|:---|
| [Azure Monitor 에이전트(미리 보기)](agents/azure-monitor-agent-overview.md) | 예 | 예 |
| [Log Analytics 에이전트](agents/log-analytics-agent.md) | 예 | 예|
| [진단 확장](agents/diagnostics-extension-overview.md) | 예 | 예 |
| [Telegraf 에이전트](essentials/collect-custom-metrics-linux-telegraf.md) | 예 | 예 |
| [종속성 에이전트](vm/vminsights-enable-overview.md) | 예 | 예 |


## <a name="product-integrations"></a>제품 통합
다음 표의 서비스 및 솔루션은 Azure Monitor에서 수집한 다른 로그 데이터를 사용하여 분석할 수 있도록 해당 데이터를 Log Analytics 작업 영역에 저장합니다.

| 제품/서비스 | Description |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Windows 및 Linux 컴퓨터에서 운영 체제 업데이트를 관리하고 변경 내용을 추적합니다. [변경 내용 추적](../automation/change-tracking/overview.md) 및 [업데이트 관리](../automation/update-management/overview.md)을 참조하세요. |
| [Azure Information Protection ](/azure/information-protection/) | 문서와 이메일을 분류하고 필요에 따라 보호합니다. [Azure Information Protection의 중요 보고](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)를 참조하세요. |
| [Azure Security Center](../security-center/index.yml) | 보안 이벤트를 수집 및 분석하고 위협 분석을 수행합니다. [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md)을 참조하세요. |
| [Azure Sentinel](../sentinel/index.yml) | Office 365 및 Amazon Web Services 클라우드 내역을 비롯한 다양한 원본에 연결합니다. [데이터 원본에 연결](../sentinel/connect-data-sources.md)을 참조하세요. |
| [Microsoft Intune](/intune/) | Azure Monitor로 로그를 보낼 진단 설정을 만듭니다. [Intune에서 스토리지, 이벤트 허브 또는 로그 분석으로 로그 데이터 전송(미리 보기)](/intune/fundamentals/review-logs-using-azure-monitor)을 참조하세요.  |
| 네트워크  | [네트워크 성능 모니터](insights/network-performance-monitor.md) - 서비스 및 애플리케이션 엔드포인트에 대한 네트워크 연결 및 성능을 모니터링합니다.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) - Azure Application Gateway에서 로그 및 메트릭을 분석합니다.<br>[트래픽 분석](../network-watcher/traffic-analytics.md) - NSG(Network Watcher 네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. |
| [Office 365](insights/solution-office-365.md) | Office 365 환경을 모니터링합니다. Azure Sentinel을 통해 향상된 온보딩이 제공되는 업데이트된 버전입니다. |
| [SQL Analytics](insights/azure-sql.md) | 여러 구독 간에 대규모로 Azure SQL 데이터베이스와 SQL 관리형 인스턴스의 성능을 모니터링합니다. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub 디바이스의 상태와 사용 현황을 추적합니다. |
| [System Center Operations Manager](/system-center/scom) | Operations Manager 에이전트에서 관리 그룹을 Azure Monitor에 연결하여 데이터를 수집합니다. [Azure Monitor에 Operations Manager 연결](agents/om-agents.md)을 참조하세요.<br> [Operations Manager 평가](insights/scom-assessment.md) 솔루션을 사용하여 System Center Operations Manager 관리 그룹의 위험 및 상태를 평가합니다. |
| [Microsoft Teams 대화방](/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft Teams 대화방 디바이스의 통합된 엔드투엔드 관리입니다. |
| [Visual Studio App Center](/appcenter/) | 애플리케이션을 빌드, 테스트 및 배포한 다음, 상태 및 사용 현황을 모니터링합니다. [App Center 및 Application Insights를 사용하여 모바일 앱 분석 시작](app/mobile-center-quickstart.md)을 참조하세요. |
| Windows | [Windows 업데이트 준수](/windows/deployment/update/update-compliance-get-started) - Windows 데스크톱 업그레이드를 평가합니다.<br>[Desktop Analytics](/configmgr/desktop-analytics/overview) - Configuration Manager와 통합되어 Windows 클라이언트의 업데이트 준비 사항에 대해 충분한 정보를 파악한 후 결정하도록 인사이트와 인텔리전스를 제공합니다. |



## <a name="other-solutions"></a>기타 솔루션
기타 솔루션은 다양한 애플리케이션 및 서비스를 모니터링하는 데 사용할 수 있지만, 활성 개발은 중지되었으며 일부 지역에서는 사용하지 못할 수 있습니다. Azure Log Analytics 데이터 수집 서비스 수준 계약에서 다룹니다.

| 해결 방법 | Description |
|:---|:---|
| [Active Directory 상태 확인](insights/ad-assessment.md) | Active Directory 환경의 위험 및 상태를 평가합니다. |
| [Active Directory 복제 상태](insights/ad-replication-status.md) | Active Directory 환경에서 복제 실패가 있는지를 정기적으로 모니터링합니다. |
| [활동 로그 분석](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Azure 로그 항목을 봅니다. |
| [DNS 분석(미리 보기)](insights/dns-analytics.md) | DNS 서버에서 Windows DNS 분석 및 감사 로그와 기타 관련 데이터를 수집, 분석 및 상관 관계를 파악합니다. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 여러 배포에서 Cloud Foundry 시스템 상태와 성능 메트릭을 수집, 확인 및 분석합니다. |
| [컨테이너](containers/containers.md) | Docker 및 Windows 컨테이너 호스트를 보고 관리합니다. |
| [주문형 평가](/services-hub/health/getting_started_with_on_demand_assessments) | 온-프레미스, 하이브리드 및 클라우드 Microsoft 기술 환경의 가용성, 보안 및 성능을 평가하고 최적화합니다. |
| [SQL 상태 검사](insights/sql-assessment.md) | SQL Server 환경의 위험 및 상태를 평가합니다.  |
| [실시간 데이터](insights/wire-data.md) | Log Analytics 에이전트를 통해 Windows 연결 및 Linux 연결 컴퓨터에서 수집되는 통합 네트워크 및 성능 데이터입니다. |

## <a name="third-party-integration"></a>타사 통합

| 해결 방법 | Description |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | ITSMC(IT 서비스 관리 커넥터)를 사용하면 Azure와 지원되는 ITSM(IT 서비스 관리) 제품/서비스를 연결할 수 있습니다.  |


## <a name="resources-outside-of-azure"></a>Azure 외부의 리소스
Azure Monitor는 다음 표에 나열된 방법을 사용하여 Azure 외부의 리소스에서 데이터를 수집할 수 있습니다.

| 리소스 | 방법 |
|:---|:---|
| 애플리케이션 | Application Insights를 사용하여 Azure 외부의 웹 애플리케이션을 모니터링합니다. [Application Insights란?](./app/app-insights-overview.md)을 참조하세요. |
| 가상 머신 | 에이전트를 사용하여 다른 클라우드 환경이나 온-프레미스에 있는 가상 머신의 게스트 운영 체제에서 데이터를 수집합니다. [Azure Monitor 에이전트 개요](agents/agents-overview.md)를 참조하세요. |
| REST API 클라이언트 | 별도의 API를 사용하여 REST API 클라이언트의 Azure Monitor 로그 및 메트릭에 데이터를 쓸 수 있습니다. 로그의 경우 [HTTP 데이터 수집기 API를 사용하여 로그 데이터를 Azure Monitor로 보내기](logs/data-collector-api.md)를, 메트릭의 경우 [REST API를 사용하여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내기](essentials/metrics-store-custom-rest-api.md)를 참조하세요. |



## <a name="next-steps"></a>다음 단계

- [인사이트 및 솔루션에서 수집한 로그 및 메트릭을 저장하는 Azure Monitor 데이터 플랫폼](data-platform.md)에 대해 자세히 알아봅니다.
- [Azure 리소스 모니터링에 대한 자습서](essentials/tutorial-resource-logs.md)를 완료합니다.
- [Azure Monitor 로그의 데이터를 분석하기 위한 로그 쿼리 작성에 대한 자습서](essentials/tutorial-resource-logs.md)를 완료합니다.
- [Azure Monitor 메트릭에서 데이터를 분석하기 위한 메트릭 차트 만들기 자습서](essentials/tutorial-metrics-explorer.md)를 완료합니다.