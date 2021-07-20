---
title: 활동 로그 경고에 사용된 웹후크 스키마 이해
description: 활동 로그 경고가 활성화될 때 웹후크 URL에 게시되는 JSON 스키마에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/31/2017
ms.openlocfilehash: e4ced623cb6f4b44aea0d8b8b275a11531a8c93a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068161"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 활동 로그 경고에 대한 웹후크
작업 그룹 정의의 일부로 활동 로그 경고 알림을 받도록 웹후크 엔드포인트를 구성할 수 있습니다. 웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 이러한 알림을 다른 시스템으로 라우팅할 수 있습니다. 이 문서는 Webhook에 대한 HTTP POST의 페이로드 형태를 보여 줍니다.

활동 로그 경고에 대한 자세한 내용은 [Azure 활동 로그 알림을 만드는](./activity-log-alerts.md) 방법을 참조하세요.

작업 그룹에 대한 자세한 내용은 [작업 그룹을 만드는](./action-groups.md) 방법을 참조하세요.

> [!NOTE]
> 웹후크 통합을 위해 Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공하는 [일반 경고 스키마](./alerts-common-schema.md)를 사용할 수도 있습니다. [일반 경고 스키마 정의에 대해 알아보세요.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>웹후크 인증
웹후크는 인증을 위해 토큰 기반 인증을 선택적으로 사용할 수 있습니다. 웹후크 URI는 토큰 ID를 통해 저장됩니다. 예: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>페이로드 스키마
POST 작업에 포함된 JSON 페이로드는 페이로드 data.context.activityLog.eventSource 필드에 따라 다릅니다.

> [!NOTE]
> 현재 활동 로그 이벤트의 일부인 설명은 발생한 **"경고 설명"** 속성에 복사됩니다.
>
> 활동 로그 페이로드를 다른 경고 유형에 맞추기 위해 2021년 4월 1일부터 발생한 경고 속성 **"설명"** 에 경고 규칙 설명이 대신 포함됩니다.
>
> 이러한 변경을 준비하기 위해 활동 로그에서 발생한 경고에 새 속성 **"활동 로그 이벤트 설명"** 을 만들었습니다. 이 새 속성은 이미 사용할 수 있는 **"설명"** 속성으로 채워집니다. 이는 새 필드 **"활동 로그 이벤트 설명"** 에 활동 로그 이벤트의 일부인 설명이 포함됨을 의미합니다.
>
> 경고 규칙, 작업 규칙, 웹후크, 논리 앱 또는 발생한 경고에서 **"설명"** 속성을 사용하고 있을 수 있는 기타 구성을 검토하고 **"활동 로그 이벤트 설명"** 속성으로 바꿉니다.
>
> 작업 규칙, 웹후크, 논리 앱 또는 기타 구성에 있는 조건이 현재 활동 로그 경고에 대한 **"설명"** 속성을 기반으로 하는 경우, 대신  **"활동 로그 이벤트 설명"** 속성을 기반으로 이를 수정해야 할 수 있습니다.
>
> 새 **"설명"** 속성을 채우기 위해 경고 규칙 정의에 설명을 추가할 수 있습니다.
> ![발생한 활동 로그 경고](media/activity-log-alerts-webhook/activity-log-alert-fired.png)

### <a name="common"></a>일반

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>관리

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>보안

```json
{
  "schemaId":"Microsoft.Insights/activityLogs",
  "data":{"status":"Activated",
    "context":{
      "activityLog":{
        "channels":"Operation",
        "correlationId":"2518408115673929999",
        "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
        "eventSource":"Security",
        "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
        "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
        "level":"Informational",
        "operationName":"Microsoft.Security/locations/alerts/activate/action",
        "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
        "properties":{
          "attackers":"[\"IP Address: 01.02.03.04\"]",
          "numberOfFailedAuthenticationAttemptsToHost":"456",
          "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
          "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
          "actionTaken":"Detected",
          "resourceType":"Virtual Machine",
          "severity":"Medium",
          "compromisedEntity":"LinuxVM1",
          "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
          "attackedResourceType":"Virtual Machine"
        },
        "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
        "resourceGroupName":"contoso",
        "resourceProviderName":"Microsoft.Security",
        "status":"Active",
        "subscriptionId":"12345-5645-123a-9867-123b45a6789",
        "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
        "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
      }
    },
    "properties":{}
  }
}
```

### <a name="recommendation"></a>권장

```json
{
  "schemaId":"Microsoft.Insights/activityLogs",
  "data":{
    "status":"Activated",
    "context":{
      "activityLog":{
        "channels":"Operation",
        "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
        "caller":"Microsoft.Advisor",
        "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
        "description":"A new recommendation is available.",
        "eventSource":"Recommendation",
        "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
        "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
        "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
        "level":"Informational",
        "operationName":"Microsoft.Advisor/recommendations/available/action",
        "properties":{
          "recommendationSchemaVersion":"1.0",
          "recommendationCategory":"HighAvailability",
          "recommendationImpact":"Medium",
          "recommendationName":"Enable Soft Delete to protect your blob data",
          "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
          "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
        },
        "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
        "resourceGroupName":"CONTOSO",
        "resourceProviderName":"MICROSOFT.STORAGE",
        "status":"Active",
        "subStatus":"",
        "subscriptionId":"12345-5645-123a-9867-123b45a6789",
        "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
        "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
      }
    },
    "properties":{}
  }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

서비스 상태 알림 활동 로그 경고에 대한 특정 스키마 세부 정보는 [서비스 상태 알림](../../service-health/service-notifications.md)을 참조하세요. 또한 [기존 문제 관리 솔루션으로 서비스 상태 웹후크 알림 구성](../../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| 요소 이름 | Description |
| --- | --- |
| 상태 |메트릭 경고에 사용됩니다. 활동 로그 경고에 대해서는 항상 “activated”로 설정합니다. |
| 컨텍스트 |이벤트에 대한 컨텍스트입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자입니다. |
| conditionType |항상 "Event"입니다. |
| name |경고 규칙의 이름입니다. |
| id |경고의 리소스 ID입니다. |
| description |경고를 만들 때 설정하는 경고 설명입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| timestamp |요청을 처리하는 Azure 서비스에서 이벤트를 생성한 시간입니다. |
| resourceId |영향을 받는 리소스의 리소스 ID입니다. |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| properties |이벤트에 대한 세부 정보를 포함하는 `<Key, Value>` 쌍의 집합(즉, `Dictionary<String, String>`)입니다. |
| event |이벤트에 대한 메타데이터가 포함된 요소입니다. |
| 권한 부여 |이벤트의 Azure 역할 기반 액세스 제어 속성입니다. 이러한 속성에는 일반적으로 action, role 및 scope이 포함됩니다. |
| category |이벤트의 범주. 지원되는 값으로 Administrative, Alert, Security, ServiceHealth, Recommendation이 있습니다. |
| caller |가용성에 기반한 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. 특정 시스템 호출의 경우 null일 수 있습니다. |
| correlationId |일반적으로 문자열 형식의 GUID. correlationId가 있는 이벤트는 동일한 상위 작업에 속하며 일반적으로 correlationId를 공유합니다. |
| eventDescription |이벤트에 대한 정적 텍스트 설명입니다. |
| eventDataId |이벤트에 대한 고유 식별자입니다. |
| eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다. |
| httpRequest |요청은 일반적으로 clientRequestId, clientIpAddress 및 HTTP 메서드(예: PUT)를 포함합니다. |
| 수준 |Critical, Error, Warning 및 Informational 값 중 하나입니다. |
| operationId |일반적으로 이벤트 간에 공유되는 GUID로서 단일 작업에 해당합니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트의 속성입니다. |
| 상태 |문자열입니다. 작업의 상태입니다. 일반적인 값으로 Started, In Progress, Succeeded, Failed, Active 및 Resolved가 포함됩니다. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값으로 OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503) 및 Gateway Timeout (HTTP Status Code: 504)이 있습니다. |

다른 모든 활동 로그 경고에 대한 특정 스키마 세부 정보는 [Azure 활동 로그 개요](../essentials/platform-logs-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [활동 로그에 대해 자세히 알아보세요](../essentials/platform-logs-overview.md).
* [Azure 경고에 대한 Azure Automation 스크립트(Runbook)를 실행하세요](https://go.microsoft.com/fwlink/?LinkId=627081).
* [논리 앱을 사용하여 Azure 경고에서 Twilio를 통해 SMS를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-text-message-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Slack 메시지를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-slack-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Azure Queue에 메시지를 보냅니다](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-queue-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
