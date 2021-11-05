---
title: Azure Monitor REST API를 통해 진단 로그 설정
description: Azure Monitor REST API 사용하여 Azure Data Factory 대한 진단 로그를 설정하는 방법을 알아봅니다.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 41b7a817b35b15ad18d962f5fb073fac76118444
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842837"
---
# <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그 설정

이 문서에서는 Azure Monitor REST API 사용하여 Azure Data Factory 대한 진단 로그를 설정하는 방법을 설명합니다.

## <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용하여 비컴퓨팅 리소스에 대한 진단 로그를 구성합니다. 리소스 컨트롤에 대한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정합니다. 예를 들면 Azure 스토리지 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송되는 로그 범주를 지정합니다.
* 각 로그 범주를 스토리지 계정에 유지해야 하는 기간을 지정합니다.
* 보존 기간이 0일이면 로그가 영구적으로 유지됩니다. 그렇지 않으면 값은 1에서 2,147,483,647 사이의 날짜 수일 수 있습니다.
* 보존 정책이 설정되어도 스토리지 계정에 로그 저장이 가능하도록 설정되지 않으면 보존 정책은 효과가 없습니다. 예를 들어 이 조건은 이벤트 허브 또는 로그 모니터링 옵션을 선택한 경우에만 발생할 수 있습니다.
* 보존 정책은 하루 단위로 적용됩니다. 하루는 UTC(협정 세계시) 자정을 기준으로 구분됩니다. 하루가 끝날 때 경과 일수가 보존 정책을 초과한 로그는 삭제됩니다. 예를 들어, 보존 정책이 1일이면 오늘이 시작될 때 어제 이전의 로그는 삭제됩니다.

## <a name="enable-diagnostic-logs-via-the-monitor-rest-api"></a>모니터 REST API 통해 진단 로그 사용

모니터 REST API 사용하여 진단 로그를 사용하도록 설정합니다.

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Monitor REST API에서 진단 설정 만들기 또는 업데이트

#### <a name="request"></a>요청

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스의 ID로 대체합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 인증 헤더를 Azure AD(Azure Active Directory)에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

#### <a name="body"></a>본문

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 속성 | Type | Description |
| --- | --- | --- |
| **storageAccountId** |String | 진단 로그를 보내려는 스토리지 계정의 리소스 ID입니다. |
| **serviceBusRuleId** |String | 스트리밍 진단 로그에 대해 이벤트 허브를 만들려는 service-bus 네임스페이스의 서비스 버스 규칙 ID입니다. 규칙 ID의 형식은 `{service bus resource ID}/authorizationrules/{key name}`입니다.|
| **workspaceId** | String | 로그가 저장될 작업 영역의 작업 영역 ID입니다. |
|**메트릭**| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| **로그**| 복합 형식| 리소스 종류에 대한 진단 로그 범주의 이름입니다. 리소스에 대한 진단 로그 범주 목록을 얻으려면 GET 진단 설정 작업을 수행합니다. |
| **category**| String| 로그 범주 및 해당 보존 정책의 배열입니다. |
| **timeGrain** | String | ISO 8601 기간 형식으로 캡처되는 메트릭의 세분성입니다. 속성 값은 1분을 지정하는 `PT1M`이어야 합니다. |
| **enabled**| 부울 | 이 리소스에 대해 메트릭 또는 로그 범주 수집을 사용할지 여부를 지정합니다. |
| **retentionPolicy**| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 이 속성은 스토리지 계정에만 사용됩니다. |
|**days**| Int| 메트릭 또는 로그를 보관할 일 수입니다. 속성 값이 0이면 로그가 영구적으로 보관됩니다. 이 속성은 스토리지 계정에만 사용됩니다. |

#### <a name="response"></a>응답

200 정상입니다.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Monitor REST API에서 진단 설정에 대한 정보 가져오기

#### <a name="request"></a>요청

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스의 ID로 대체합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 인증 헤더를 Azure AD에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

#### <a name="response"></a>응답

200 정상입니다.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
자세한 내용은 [진단 설정을 참조하세요.](/rest/api/monitor/diagnosticsettings)

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용하여 SSIS 작업 모니터링](monitor-ssis.md)
