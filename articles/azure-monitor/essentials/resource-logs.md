---
title: Azure 리소스 로그
description: Azure Monitor에서 Log Analytics 작업 영역으로 리소스 로그를 스트리밍하는 방법을 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: ac4ada3fe234428216d50edd85caa16fed10a5d2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231573"
---
# <a name="azure-resource-logs"></a>Azure 리소스 로그
Azure 리소스 로그는 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공하는 [플랫폼 로그](../essentials/platform-logs-overview.md)입니다. 이러한 로그의 내용은 Azure 서비스와 리소스 종류에 따라 달라집니다. 리소스 로그는 기본적으로 수집되지 않습니다. 각 Azure 리소스에 대한 진단 설정을 만들어 [Azure Monitor Logs](../logs/data-platform-logs.md)에 사용하기 위한 Log Analytics 작업 영역으로 전송하거나, Azure 외부로 전달하기 위해 Azure Event Hubs로 전송하거나, 보관을 위해 Azure Storage로 전송합니다.

진단 설정 만들기에 대한 자세한 내용은 [플랫폼 로그 및 메트릭을 다양한 대상에 전송하는 진단 설정 만들기](../essentials/diagnostic-settings.md)를, Azure Policy를 사용하여 만드는 각 Azure 리소스에 대한 진단 설정을 자동으로 만드는 방법에 대한 자세한 내용은 [Azure Policy를 통한 대규모 Azure Monitor 배포](../best-practices.md)를 참조하세요.

## <a name="send-to-log-analytics-workspace"></a>Log Analytics 작업 영역으로 보내기
 다음을 포함하는 [Azure Monitor Logs](../logs/data-platform-logs.md) 기능을 사용할 수 있도록 리소스 로그를 Log Analytics 작업 영역으로 전송합니다.

- Azure Monitor에서 수집한 다른 모니터링 데이터와 리소스 로그 데이터의 상관관계를 파악합니다.
- 여러 Azure 리소스, 구독, 테넌트의 로그 항목을 분석을 위해 한곳에 취합합니다.
- 로그 쿼리를 사용하여 복잡한 분석을 수행하고 로그 데이터에 대한 심층적인 인사이트를 확보합니다.
- 경고 논리가 복잡한 로그 경고를 사용합니다.

리소스 로그를 Log Analytics 작업 영역으로 전송하는 [진단 설정을 만듭니다](../essentials/diagnostic-settings.md). 이 데이터는 [Azure Monitor Logs의 구조](../logs/data-platform-logs.md)에 설명된 대로 테이블에 저장됩니다. 리소스 로그에서 사용하는 테이블은 리소스에서 사용하는 컬렉션 유형에 따라 달라집니다.

- Azure 진단 - 작성된 모든 데이터는 [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블에 저장됩니다.
- 리소스 관련 - 데이터가 리소스의 각 범주에 대한 개별 테이블에 작성됩니다.

### <a name="azure-diagnostics-mode"></a>Azure 진단 모드 
이 모드에서는 모든 진단 설정의 모든 데이터가 [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블에 수집됩니다. 이는 오늘날 대부분의 Azure 서비스에서 사용하는 레거시 방식입니다. 여러 리소스 형식이 동일한 테이블에 데이터를 전송하기 때문에 해당 스키마는 수집되는 다른 모든 데이터 형식 스키마의 상위 세트입니다. 이 테이블의 구조와 잠재적으로 많은 수의 열에서 작동하는 원리에 대한 자세한 내용은 [AzureDiagnostics 참조](/azure/azure-monitor/reference/tables/azurediagnostics)를 참조하세요.

진단 설정이 다음 데이터 형식에 대해 동일한 작업 영역에서 수집되는 다음 예제를 살펴보세요.

- 서비스 1의 감사 로그(A, B, C 열로 구성된 스키마 포함)  
- 서비스 1의 오류 로그(D, E, F 열로 구성된 스키마 포함)  
- 서비스 2의 감사 로그(G, H, I 열로 구성된 스키마 포함)  

AzureDiagnostics 테이블은 다음과 같습니다.  

| ResourceProvider    | 범주     | A  | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>리소스 관련(Resource-Specific)
이 모드에서는 진단 설정에서 선택한 각 범주에 대해 선택한 작업 영역의 개별 테이블이 생성됩니다. 이 방법이 권장되는 이유는 로그 쿼리의 데이터를 훨씬 쉽게 처리할 수 있고, 스키마 및 해당 구조를 보다 효과적으로 검색하며, 수집 대기 시간과 쿼리 시간의 성능이 모두 향상되고, 특정 테이블에서 Azure RBAC 권한을 부여할 수 있기 때문입니다. 모든 Azure 서비스는 결국 Resource-Specific 모드로 마이그레이션됩니다. 

위의 예제에서는 세 개의 테이블이 생성됩니다.
 
- *Service1AuditLogs* 테이블은 다음과 같습니다.

    | 리소스 공급자 | 범주 | A | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- *Service1ErrorLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | 범주 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- *Service2AuditLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | 범주 | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>컬렉션 모드 선택
대부분의 Azure 리소스는 선택권을 주지 않고 **Azure Diagnostic** 또는 **Resource-Specific 모드** 의 작업 영역에 데이터를 작성합니다. 사용 모드에 대한 자세한 내용은 [각 서비스에 대한 설명서](./resource-logs-schema.md)를 참조하세요. 모든 Azure 서비스는 결국 Resource-Specific 모드를 사용합니다. 이러한 전환의 일부로, 일부 리소스는 진단 설정에서 모드를 선택할 수 있습니다. 리소스 관련 모드를 사용하면 데이터를 더 쉽게 관리하고 향후 복잡한 마이그레이션을 방지할 수 있으므로 모든 새 진단 설정에 리소스 관련 모드를 지정합니다.
  
   ![진단 설정 모드 선택기](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> 리소스 관리자 템플릿을 사용하여 컬렉션 모드를 설정하는 예제는 [Azure Monitor에서 진단 설정을 위한 Resource Manager 템플릿 샘플](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault)을 참조하세요.


기존 진단 설정을 리소스 관련 모드로 변경할 수 있습니다. 이 경우 이미 수집된 데이터는 작업 영역의 데이터 보존 설정에 따라 삭제될 때까지 _AzureDiagnostics_ 테이블에 남아 있습니다. 새 데이터는 전용 테이블에 수집됩니다. [합집합](/azure/kusto/query/unionoperator) 연산자를 사용하여 두 테이블에서 데이터를 쿼리합니다.

Resource-Specific 모드를 지원하는 Azure 서비스에 대한 공지 사항은 [Azure Updates](https://azure.microsoft.com/updates/) 블로그를 참조하세요.


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs로 전송
리소스 로그를 Azure 외부(예: 타사 SIEM 또는 기타 로그 분석 솔루션)로 전송하려면 이벤트 허브로 전송합니다. 이벤트 허브의 리소스 로그는 각 페이로드에 레코드를 포함하는 `records` 요소를 사용하여 JSON 형식으로 사용됩니다. 이 스키마는 [Azure Resource Logs에 대한 공통 및 서비스 관련 스키마](resource-logs-schema.md)에 설명된 대로 리소스 유형에 따라 달라집니다.

다음은 리소스 로그에 대한 Event Hubs의 샘플 출력 데이터입니다.

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Azure Storage에 보내기
보관을 위해 Azure 스토리지로 리소스 로그를 보냅니다. 진단 설정을 만들면 사용 설정된 로그 범주 중 하나에서 이벤트가 발생하는 즉시 스토리지 계정에서 스토리지 컨테이너가 만들어집니다. 컨테이너 내의 Blob은 다음과 같은 명명 규칙을 사용합니다.

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 네트워크 보안 그룹에 대한 Blob의 이름은 다음과 유사할 수 있습니다.

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 리소스 로그 이벤트는 시간당 개별 Blob으로 나뉘므로 분 값(m=00)은 항상 00입니다.

PT1H.json 파일 내에서 각 이벤트는 다음 형식으로 저장됩니다. [리소스 로그 스키마](./resource-logs-schema.md)에 설명된 대로 공통 최상위 스키마를 사용하지만 Azure 서비스마다 다릅니다.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 플랫폼 로그는 [JSON 줄](http://jsonlines.org/)을 사용하여 Blob Storage에 작성됩니다. 여기서 각 이벤트는 줄이며 줄 바꿈 문자는 새 이벤트를 나타냅니다. 이 형식은 2018년 11월에 구현되었습니다. 이 날짜 이전에는 [스토리지 계정에 보관된 Azure Monitor 플랫폼 로그에 대한 형식 변경 준비](resource-logs-blob-format.md)에 설명된 대로 로그가 Blob Storage에 레코드의 json 배열로 작성되었습니다.


## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대한 자세한 정보](../essentials/platform-logs-overview.md).
* [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../essentials/diagnostic-settings.md)