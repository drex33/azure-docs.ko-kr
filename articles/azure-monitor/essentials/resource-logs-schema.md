---
title: Azure 리소스 로그 지원 서비스 및 스키마
description: Azure 리소스 로그에 대해 지원되는 서비스 및 이벤트 스키마를 이해합니다.
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 99746b8f392d8afc5df9aa14ac7e1c7f19069151
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454984"
---
# <a name="common-and-service-specific-schemas-for-azure-resource-logs"></a>Azure 리소스 로그에 대한 일반 및 서비스별 스키마

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그라고 했습니다. Azure Monitor에서 수집하는 로그 유형이 Azure 리소스 이상을 포함하도록 전환하면서 2019년 10월에 이름이 변경되었습니다. 
>
> 이 문서에서는 수집할 수 있는 리소스 로그 범주를 나열하는 데 사용됩니다. 해당 목록은 이제 리소스 로그 범주 에 [있습니다.](resource-logs-categories.md) 

[Azure Monitor 리소스 로그](../essentials/platform-logs-overview.md)는 해당 서비스 또는 리소스의 작업을 설명하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor 통해 사용할 수 있는 모든 리소스 로그는 공통 최상위 스키마를 공유합니다. 각 서비스는 고유한 이벤트에 대한 고유한 속성을 유연하게 내보낸다.

리소스 종류(속성에서 사용 `resourceId` 가능)와 범주의 조합은 스키마를 고유하게 식별합니다. 이 문서에서는 리소스 로그에 대한 최상위 스키마와 각 서비스의 스키마에 대한 링크를 설명합니다.


## <a name="top-level-common-schema"></a>최상위 공통 스키마

| Name | 필수 또는 선택 | Description |
|---|---|---|
| `time` | 필수 | 이벤트의 타임스탬프(UTC)입니다. |
| `resourceId` | 필수 | 이벤트를 내보낸 리소스의 리소스 ID입니다. 테넌트 서비스의 경우 */tenants/tenant-id/providers/provider-name* 형식입니다. |
| `tenantId` | 테넌트 로그에 필요 | 이 이벤트가 연결된 Active Directory 테넌트의 테넌트 ID입니다. 이 속성은 테넌트 수준 로그에만 사용됩니다. 리소스 수준 로그에는 표시되지 않습니다. |
| `operationName` | 필수 | 이 이벤트가 나타내는 작업의 이름입니다. 이벤트가 Azure RBAC(역할 기반 액세스 제어) 작업을 나타내는 경우 Azure RBAC 작업 이름(예: `Microsoft.Storage/storageAccounts/blobServices/blobs/Read` )입니다. 이 이름은 문서화된 Resource Manager 작업이 아닌 경우에도 일반적으로 Azure Resource Manager 작업의 형태로 모델링됩니다( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ). |
| `operationVersion` | 선택 사항 | 가 API를 통해 수행된 경우 작업과 연결된 `operationName` API 버전입니다(예: `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). 이 작업에 해당하는 API가 없는 경우 버전은 나중에 작업과 연결된 속성이 변경될 경우 해당 작업의 버전을 나타냅니다. |
| `category` | 필수 | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. 일반적인 로그 범주는 `Audit` , `Operational` , 및 `Execution` `Request` 입니다. |
| `resultType` | 선택 사항 | 이벤트의 상태입니다. 일반적인 값은 `Started` , , , , 및 `In Progress` `Succeeded` `Failed` `Active` `Resolved` 입니다. |
| `resultSignature` | 선택 사항 | 이벤트의 하위 통계입니다. 이 작업이 REST API 호출에 해당하는 경우 이 필드는 해당 REST 호출의 HTTP 상태 코드입니다. |
| `resultDescription `| 선택 사항 | 이 작업에 대한 정적 텍스트 설명입니다. 예를 들어 `Get storage file` 입니다. |
| `durationMs` | 선택 사항 | 밀리초 단위의 작업 기간입니다. |
| `callerIpAddress` | 선택 사항 | 작업이 공용 IP 주소를 사용하는 엔터티에서 시작되는 API 호출에 해당하는 경우 호출자 IP 주소입니다. |
| `correlationId` | 선택 사항 | 관련된 이벤트 집합을 그룹화하기 위해 사용되는 GUID입니다. 일반적으로 두 이벤트의 `operationName` 값은 같지만 상태(예: 및 `Started` `Succeeded` )는 동일한 `correlationID` 값을 공유합니다. 이는 이벤트 간의 다른 관계를 나타낼 수도 있습니다. |
| `identity` | 선택 사항 | 작업을 수행한 사용자 또는 애플리케이션의 ID를 설명하는 JSON Blob입니다. 일반적으로 이 필드에는 Active Directory의 권한 부여 및 클레임 또는 JWT 토큰이 포함됩니다. |
| `Level` | 선택 사항 | 이벤트의 심각도 수준입니다. `Informational`, `Warning`, `Error` 또는 `Critical` 중에 하나여야 합니다. |
| `location` | 선택 사항 | 이벤트를 내보내는 리소스의 영역입니다. 예를 들어 `East US` 또는 `France South` 입니다. |
| `properties` | 선택 사항 | 이 이벤트 범주와 관련된 모든 확장 속성입니다. 모든 사용자 지정 또는 고유 속성은 스키마의 이 "파트 B" 내에 배치해야 합니다. |

## <a name="service-specific-schemas"></a>서비스별 스키마

리소스 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다. 다음 목록에서는 사용 가능한 리소스 로그를 만드는 Azure 서비스 및 서비스 및 범주별 스키마(사용 가능한 경우)에 대한 링크를 보여줍니다. 새 서비스가 추가되면 목록이 변경됩니다. 필요한 항목이 보이지 않으면 이 문서에서 GitHub 문제를 자유롭게 열어 업데이트할 수 있습니다.

| 서비스 또는 기능 | 스키마 및 설명서 |
| --- | --- |
| Azure Active Directory | [개요](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [감사 로그 스키마](../../active-directory/reports-monitoring/overview-reports.md), [로그인 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Azure Analysis Services | [Azure Analysis Services: 진단 로깅 설정](../../analysis-services/analysis-services-logging.md) |
| Azure API Management | [리소스 로그 API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Azure App Service | [로그 App Service](../../app-service/troubleshoot-diagnostic-logs.md)
| Azure Application Gateway |[Application Gateway에 대한 로깅](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics for Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 로깅](../../batch/batch-diagnostics.md) |
| Azure Cognitive Services | [Azure Cognitive Services에 대한 로깅](../../cognitive-services/diagnostic-logging.md) |
| Azure Container Instances | [Azure Container Instances에 대한 로깅](../../container-instances/container-instances-log-analytics.md#log-schema) |
| Azure Container Registry | [Azure Container Registry에 대한 로깅](../../container-registry/monitor-service.md) |
| Azure Content Delivery Network | [Azure Content Delivery Network 대한 진단 로그](../../cdn/cdn-azure-diagnostic-logs.md) |
| Azure Cosmos DB | [Azure Cosmos DB 로깅](../../cosmos-db/monitor-cosmos-db.md) |
| Azure Data Explorer | [Azure Data Explorer 로그](/azure/data-explorer/using-diagnostic-logs) |
| Azure 데이터 팩터리 | [Azure Monitor 사용하여 Data Factory 모니터링](../../data-factory/monitor-using-azure-monitor.md) |
| Azure 데이터 레이크 분석 |[Azure Data Lake Analytics에 대한 로그 액세스](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Azure Data Lake Storage |[Azure Data Lake Storage 대한 로그 액세스](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Database for MySQL | [Azure Database for MySQL 진단 로그](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 로그](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Azure Databricks의 진단 로깅](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure DDoS Protection | [Azure DDoS Protection 표준에 대한 로깅](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Azure Digital Twins 진단 설정](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Azure Event Hubs |[Azure Event Hubs 로그](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Azure ExpressRoute | 스키마를 사용할 수 없습니다. |
| Azure Firewall | [Azure Firewall에 대한 로깅](../../firewall/logs-and-metrics.md#diagnostic-logs) |
| Azure Front Door | [Azure Front Door 대한 로깅](../../frontdoor/front-door-diagnostics.md) |
| Azure IoT Hub | [IoT Hub 작업](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Azure Key Vault |[Azure Key Vault 로깅](../../key-vault/general/logging.md) |
| Azure Kubernetes Service |[Azure Kubernetes Service 로깅](../../aks/monitor-aks-reference.md#resource-logs) |
| Azure Load Balancer |[log Analytics for Azure Load Balancer](../../load-balancer/monitor-load-balancer.md) |
| Azure Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Machine Learning | [Azure Machine Learning의 진단 로깅](../../machine-learning/monitor-resource-reference.md) |
| Azure Media Services | [모니터링 스키마 Media Services](../../media-services/latest/monitoring/monitor-media-services-data-reference.md#schemas) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)용 Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Azure Power BI Embedded | [Azure의 Power BI Embedded에 대한 로깅](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup 데이터 모델](../../backup/backup-azure-reports-data-model.md)|
| 트래픽 분석 검색 |[검색 트래픽 분석 사용 및 사용](../../search/search-traffic-analytics.md) |
| Azure Service Bus |[Azure Service Bus 로그](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Azure SQL Database | [Azure SQL Database 로깅](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Azure Storage | [Blob](../../storage/blobs/monitor-blob-storage-reference.md#resource-logs-preview), [파일](../../storage/files/storage-files-monitoring-reference.md#resource-logs-preview), [큐](../../storage/queues/monitor-queue-storage-reference.md#resource-logs-preview), [테이블](../../storage/tables/monitor-table-storage-reference.md#resource-logs-preview) |
| Azure Stream Analytics |[작업 로그](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Azure Traffic Manager | [로그 스키마 Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Azure Virtual Network | 스키마를 사용할 수 없습니다. |
| 가상 네트워크 게이트웨이 | 스키마를 사용할 수 없습니다. |



## <a name="next-steps"></a>다음 단계

* [수집할 수 있는 리소스 로그 범주 참조](resource-logs-categories.md)
* [리소스 로그에 대해 자세히 알아보기](../essentials/platform-logs-overview.md)
* [리소스 로그를 Event Hubs 스트림](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API 사용하여 리소스 로그 진단 설정 변경](/rest/api/monitor/diagnosticsettings)
* [Log Analytics를 사용하여 Azure Storage 로그 분석](./resource-logs.md#send-to-log-analytics-workspace)