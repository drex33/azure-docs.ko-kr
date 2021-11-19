---
title: Azure Event Grid - Event Grid 리소스에 대한 진단 로그 사용
description: 이 문서에서는 Event Grid 리소스에 진단 로그를 사용하도록 설정하는 방법에 대한 단계별 지침을 제공합니다.
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 06d17d257955dab7c51e24d930eea3a60ca667ce
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808131"
---
# <a name="enable-diagnostic-logs-for-event-grid-resources"></a>Event Grid 리소스에 대한 진단 로그 사용

이 문서에서는 Event Grid 리소스에 대한 진단 설정을 사용하도록 설정하기 위한 단계별 지침을 제공합니다. 이러한 설정을 사용하면 모든 오류를 해결할 수 있도록 진단 정보를 캡처하고 볼 수 있습니다. 다음 표에서는 사용자 지정 항목, 시스템 토픽 및 도메인과 같은 다양한 유형의 Event Grid 리소스에 사용할 수 있는 설정을 보여 있습니다.

| 진단 설정 |  Event Grid 항목 | Event Grid 시스템 항목 | Event Grid 도메인 |
| ------------- |  --------- | ----------- | ----------- |
| [DeliveryFailures](diagnostic-logs.md#schema-for-publishdelivery-failure-logs)  | 예 | 예 | 예 |
| [PublishFailures](diagnostic-logs.md#schema-for-publishdelivery-failure-logs)  | 예 | 예 | 예 |
| [DataPlaneRequests](diagnostic-logs.md#schema-for-data-plane-requests) | 예 | 아니요 | 예 |

> [!IMPORTANT]
> 배달 실패, 게시 실패 및 데이터 평면 요청의 스키마는 [진단 로그를 참조하세요.](diagnostic-logs.md) 

## <a name="prerequisites"></a>사전 요구 사항

- 프로비전된 Event Grid 리소스 
- 진단 로그를 캡처하기 위한 프로비전된 대상:
  - Log Analytics 작업 영역
  - 스토리지 계정
  - Event Hubs
  - 파트너 솔루션

## <a name="enable-diagnostic-logs-for-event-grid-topics-and-domains"></a>Event Grid 항목 및 도메인에 대한 진단 로그 사용

> [!NOTE]
> 다음 절차에서는 항목에 대한 진단 로그를 사용하도록 설정하는 단계별 지침을 제공합니다. 도메인에 대한 진단 로그를 사용하도록 설정하는 단계는 매우 유사합니다. 2단계에서 Azure Portal Event Grid **도메인으로** 이동합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 진단 로그 설정을 사용하도록 설정할 Event Grid 항목으로 이동합니다.
    1. 위쪽의 검색 표시줄에서 **Event Grid 항목을 검색합니다.**
        ![사용자 지정 토픽 검색](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    2. 목록에서 진단 설정을 구성할 **토픽** 을 선택합니다.
3. 왼쪽 메뉴의 **모니터링** 에서 **진단 설정** 을 선택합니다.
4. **진단 설정** 페이지에서 **새 진단 설정 추가** 를 선택합니다.
    ![진단 설정 추가 단추](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 진단 설정의 **이름** 을 지정합니다.
6. 로그 섹션에서 **allLogs**  옵션을 선택합니다.
    ![오류 선택](./media/enable-diagnostic-logs-topic/log-failures.png)
7. 로그에 대해 하나 이상의 캡처 대상을 사용하도록 설정한 다음 이전에 만든 캡처 리소스를 선택하여 구성합니다.
    - **스토리지 계정에 보관** 을 선택한 경우 **스토리지 계정 - 구성** 을 선택한 다음 Azure 구독에서 스토리지 계정을 선택합니다.
        !["Azure 스토리지 계정에 보관"이 선택되고 스토리지 계정이 선택된 "진단 설정" 페이지를 보여 주는 스크린샷.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **이벤트 허브로 스트림** 을 선택한 경우 **이벤트 허브 - 구성** 을 선택한 다음 Event Hubs 네임스페이스, 이벤트 허브 및 액세스 정책을 선택합니다.
        ![“이벤트 허브로 스트림”이 선택된 “진단 설정” 페이지를 보여 주는 스크린샷.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **Log Analytics로 보내기** 를 선택한 경우 Log Analytics 작업 영역을 선택합니다.
        ![“Log Analytics로 보내기”가 선택된 “진단 설정” 페이지를 보여 주는 스크린샷.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. **저장** 을 선택합니다. 그런 다음 오른쪽 모서리에서 **X** 를 선택하여 페이지를 닫습니다.
9. 이제 **진단 설정** 페이지로 돌아가서 **진단 설정** 테이블에 새 항목이 표시되는지 확인합니다.
    ![“진단 설정” 테이블에서 새 항목이 강조 표시된 “진단 설정” 페이지를 보여 주는 스크린샷.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

토픽에 대한 모든 메트릭의 컬렉션을 사용하도록 설정할 수도 있습니다.

## <a name="enable-diagnostic-logs-for-event-grid-system-topics"></a>Event Grid 시스템 항목에 대한 진단 로그 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 진단 로그 설정을 사용하도록 설정할 시스템 항목으로 이동합니다.
    1. 위쪽의 검색 표시줄에서 **Event Grid 시스템 항목을 검색합니다.**
        ![시스템 토픽 검색](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 진단 설정을 구성할 **시스템 토픽** 을 선택합니다.
        ![시스템 토픽 선택](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 왼쪽 메뉴의 **모니터링** 에서 **진단 설정** 을 선택한 다음 **+ 진단 설정 추가** 를 선택합니다.
    ![진단 설정 추가 - 단추](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 진단 설정의 **이름** 을 지정합니다.
5. 로그 섹션에서 **allLogs**  옵션을 선택합니다.
    ![배달 실패 선택](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. 로그에 대해 하나 이상의 캡처 대상을 사용하도록 설정한 다음 이전에 만든 캡처 리소스를 선택하여 구성합니다.
    - **Log Analytics로 보내기** 를 선택한 경우 Log Analytics 작업 영역을 선택합니다.
        ![Log Analytics에 보내기](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png)
    - **스토리지 계정에 보관** 을 선택한 경우 **스토리지 계정 - 구성** 을 선택한 다음 Azure 구독에서 스토리지 계정을 선택합니다.
        ![Azure Storage 계정에 보관](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - **이벤트 허브로 스트리밍을** 선택하는 경우 **이벤트 허브 - 구성을** 선택한 다음, Event Hubs 네임스페이스, 이벤트 허브 및 액세스 정책을 선택합니다.
        ![이벤트 허브로 스트림](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
7. **저장** 을 선택합니다. 그런 다음 오른쪽 모서리에서 **X** 를 선택하여 페이지를 닫습니다.
8. 이제 **진단 설정** 페이지로 돌아가서 **진단 설정** 테이블에 새 항목이 표시되는지 확인합니다.
    ![목록의 진단 설정](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

시스템 토픽에 대한 모든 **메트릭** 의 컬렉션을 사용하도록 설정할 수도 있습니다.
    ![시스템 토픽 - -모든 메트릭 사용](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Azure Storage에서 진단 로그 보기

1. 스토리지 계정을 캡처 대상으로 사용하도록 설정하면 Event Grid에서 진단 로그 내보내기를 시작합니다. 스토리지 계정에 **insights-logs-deliveryfailures** 및 **insights-logs-publishfailures** 라는 새 컨테이너가 표시되어야 합니다.

    ![스토리지 - 진단 로그용 컨테이너](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 컨테이너 중 하나를 탐색하면 JSON 형식의 Blob을 찾게 됩니다. 이 파일은 배달 실패 또는 게시 실패에 대한 로그 항목을 포함합니다. 탐색 경로는 Event Grid 토픽의 **ResourceId** 와 로그 항목을 내보낸 시간에 대한 타임스탬프(분 수준)를 나타냅니다. 최종적으로 다운로드할 수 있는 Blob/JSON 파일은 다음 섹션에 설명된 스키마를 따릅니다.

    ![스토리지의 JSON 파일](./media/enable-diagnostic-logs-topic/select-json.png)
3. JSON 파일에 다음 예와 유사한 내용이 표시되어야 합니다.

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

다음은 Event Grid 항목에 대한 진단 설정을 사용하도록 설정하는 샘플 Azure Resource Manager 템플릿입니다. 샘플 템플릿을 배포하면 다음 리소스가 생성됩니다.

- Event Grid 토픽
- Log Analytics 작업 영역

그런 다음, 진단 정보를 Log Analytics 작업 영역으로 보내는 항목에 대한 진단 설정을 생성합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "topic_name": {
            "defaultValue": "spegrid0917topic",
            "type": "String"
        },
        "log_analytics_workspace_name": {
            "defaultValue": "splogaw0625",
            "type": "String"
        },
        "location": {
            "defaultValue": "eastus",
            "type": "String"
        },
        "sku": {
            "defaultValue": "Free",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/topics",
            "apiVersion": "2020-10-15-preview",
            "name": "[parameters('topic_name')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Basic"
            },
            "kind": "Azure",
            "identity": {
                "type": "None"
            },
            "properties": {
                "inputSchema": "EventGridSchema",
                "publicNetworkAccess": "Enabled"
            }
        },
        {
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('log_analytics_workspace_name')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.OperationalInsights/workspaces",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                }
            }
        },
        {
            "type": "Microsoft.EventGrid/topics/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('topic_name'), '/', 'Microsoft.Insights/', parameters('log_analytics_workspace_name'))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/topics', parameters('topic_name'))]",
                "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]",
                "metrics": [
                    {
                        "category": "AllMetrics",
                        "enabled": true
                    }
                ],
                "logs": [
                    {
                        "category": "DeliveryFailures",
                        "enabled": true
                    },
                    {
                        "category": "PublishFailures",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

## <a name="enable-diagnostic-logs-for-audit-traces"></a>감사 추적에 진단 로그 사용

Event Grid 데이터 평면 작업에 대한 감사 추적을 게시할 수 있습니다. 이 기능을 사용하도록 설정하려면 **범주 그룹** 섹션에서 **감사를** 선택하거나 범주 **섹션에서** **DataPlaneRequests를** 선택합니다.

감사 추적을 사용하여 권한 있는 용도로만 데이터 액세스가 허용되도록 할 수 있습니다. 리소스 이름, 작업 유형, 네트워크 액세스, 수준, 지역 등과 같은 보안 제어에 대한 정보를 수집합니다. 진단 설정을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Event Grid 항목의 진단 로그 및 도메인 Event Grid 참조하세요.](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-event-grid-topics-and-domains)
![감사 추적 선택](./media/enable-diagnostic-logs-topic/enable-audit-logs.png)

> [!IMPORTANT]
> 스키마에 대한 자세한 내용은 `DataPlaneRequests` [진단 로그를 참조하세요.](diagnostic-logs.md)

## <a name="next-steps"></a>다음 단계

토픽 또는 도메인의 진단 로그에 대한 로그 스키마 및 기타 개념 정보는 [진단 로그](diagnostic-logs.md)를 참조하세요.
