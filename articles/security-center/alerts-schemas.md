---
title: 클라우드 경고에 대 한 Microsoft Defender의 스키마
description: 이 문서에서는 보안 경고에 대해 Microsoft Defender for Cloud에서 사용 되는 다양 한 스키마에 대해 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 335f20150490d9f792f9ba552b13b01e64e95673
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010615"
---
# <a name="security-alerts-schemas"></a>보안 경고 스키마

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

구독에서 향상 된 보안 기능을 사용할 수 있는 경우 Defender for Cloud가 해당 리소스에 대 한 위협을 검색할 때 보안 경고를 받게 됩니다.

클라우드 페이지 ( [개요 대시보드](overview-page.md), [경고](tutorial-security-incident.md), [리소스 상태 페이지](investigate-resource-health.md)또는 [워크 로드 보호 대시보드](workload-protections-dashboard.md) )와 외부 도구 (예:

- [Microsoft 센티널](../sentinel/index.yml) -microsoft의 클라우드 기본 siem. 센티널 커넥터는 Microsoft Defender for Cloud에서 경고를 가져오고 Microsoft 센티널의 [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md) 으로 보냅니다.
- 타사 SIEM - [Azure Event Hubs](../event-hubs/index.yml)로 데이터를 전송합니다. 그런 다음 Event Hub 데이터를 타사 SIEM과 통합합니다. [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)에서 자세히 알아봅니다.
- [REST API](/rest/api/securitycenter/) - REST API를 사용하여 경고에 액세스하는 경우 [온라인 경고 API 문서](/rest/api/securitycenter/alerts)를 참조하세요.

프로그래매틱 방식을 통해 경고를 사용하는 경우 적절한 스키마를 사용하여 관련 필드를 찾아야 합니다. 또한 Event Hub로 내보내거나 일반 HTTP 커넥터를 사용하여 Workflow Automation 트리거를 시도하는 경우 스키마를 사용하여 JSON 개체를 올바르게 구문 분석합니다.

>[!IMPORTANT]
> 시나리오마다 스키마가 약간 다르므로 아래에서 관련 탭을 선택해야 합니다.


## <a name="the-schemas"></a>스키마 


### <a name="microsoft-sentinel"></a>[Microsoft 센티널](#tab/schema-sentinel)

센티널 커넥터는 Microsoft Defender for Cloud에서 경고를 가져오고 Microsoft 센티널의 Log Analytics 작업 영역으로 보냅니다.

클라우드 경고에 대해 Defender를 사용 하 여 Microsoft 센티널 사례 또는 인시던트를 만들려면 아래에 표시 된 해당 경고에 대 한 스키마가 필요 합니다. 

Microsoft 센티널에 대 한 자세한 내용은 [설명서](../sentinel/index.yml)를 참조 하세요.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]


### <a name="azure-activity-log"></a>[Azure 활동 로그](#tab/schema-activitylog)

Microsoft Defender for Cloud는 Azure 활동 로그에서 이벤트로 생성 된 보안 경고를 감사 합니다.

다음과 같이 경고 활성화 이벤트를 검색하여 활동 로그에서 보안 경고 이벤트를 볼 수 있습니다.

[![활동 로그에서 활성화 경고 이벤트 검색.](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Azure 활동 로그로 전송되는 경고 샘플 JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|Description|
|----|----|
|**channels**|상수, ‘Operation’|
|**correlationId**|클라우드 용 Microsoft Defender 경고 ID|
|**description**|경고에 대한 설명|
|**eventDataId**|correlationId 참조|
|**eventName**|value 및 localizedValue 하위 필드에는 경고 표시 이름 포함|
|**category**|value 및 localizedValue 하위 필드는 상수 - ‘Security’|
|**eventTimestamp**|경고가 생성된 시점의 UTC 타임스탬프|
|**id**|정규화된 경고 ID|
|**level**|상수, ‘Informational’|
|**operationId**|correlationId 참조|
|**operationName**|값 필드는 상수 - ‘Microsoft.Security/locations/alerts/activate/action’이며, 로컬화된 값은 ‘Activate Alert’(사용자 로캘에 따라 로컬화될 수 있음)|
|**resourceGroupName**|리소스 그룹 이름 포함|
|**resourceProviderName**|value 및 localizedValue 하위 필드는 상수 - ‘Microsoft.Security’|
|**resourceType**|value 및 localizedValue 하위 필드는 상수 - ‘Microsoft.Security/locations/alerts’|
|**resourceId**|정규화된 Azure 리소스 ID|
|**status**|value 및 localizedValue 하위 필드는 상수 - ‘Active’|
|**subStatus**|value 및 localizedValue 하위 필드는 비어 있음|
|**submissionTimestamp**|활동 로그에 대한 이벤트 제출의 UTC 타임스탬프|
|**subscriptionId**|손상된 리소스의 구독 ID|
|**properties**|경고와 관련된 추가 속성의 JSON 모음. 경고마다 달라질 수 있지만 모든 경고에는 다음 필드가 표시됩니다.<br>- severity – 공격의 심각도<br>- compromisedEntity: 손상된 리소스의 이름<br>- remediationSteps: 수행할 수정 단계<br>- intent: 경고의 종료 체인 의도. 가능한 의도는 [의도 테이블](alerts-reference.md#intentions)을 참조하세요.|
|**relatedEvents**|상수 - 빈 배열|
|||


### <a name="workflow-automation"></a>[워크플로 자동화](#tab/schema-workflow-automation)

워크플로 자동화를 사용하는 경우 경고 스키마는 [커넥터 설명서](/connectors/ascalert/)를 참조하세요.


### <a name="continuous-export"></a>[연속 내보내기](#tab/schema-continuousexport)

클라우드의 연속 내보내기 기능에 대 한 Defender는 다음으로 경고 데이터를 전달 합니다.

- [경고 API](/rest/api/securitycenter/alerts)와 동일한 스키마를 사용하는 Azure Event Hub.
- Azure Monitor 데이터 참조 설명서의 [SecurityAlert 스카마](/azure/azure-monitor/reference/tables/SecurityAlert)에 따른 Log Analytics 작업 영역.




### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph는 Microsoft 365의 데이터 및 인텔리전스에 대한 게이트웨이입니다. Microsoft 365, Windows 10, Enterprise Mobility + Security에서 방대한 양의 데이터에 액세스하는 데 사용할 수 있는 통합 프로그래밍 기능 모델을 제공합니다. Microsoft Graph의 풍부한 데이터를 사용하여 수백만 명의 사용자와 상호작용하는 조직 및 소비자용 앱을 빌드합니다.

MS Graph로 전송되는 보안 경고에 대한 스키마 및 JSON 표현은 [Microsoft Graph 문서](/graph/api/resources/alert)를 참조하세요.

---


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 경고 정보를 보낼 때 클라우드의 위협 방지 도구에 사용 되는 스키마에 대해 설명 했습니다.

클라우드의 외부 Defender에서 보안 경고에 액세스 하는 방법에 대 한 자세한 내용은 다음 페이지를 참조 하세요.

- [Microsoft 센티널](../sentinel/index.yml) -microsoft의 클라우드-기본 siem
- [Azure Event Hubs](../event-hubs/index.yml) - Microsoft의 완전 관리형 실시간 데이터 수집 서비스
- [클라우드 데이터를 위해 계속 Defender를 내보냅니다.](continuous-export.md)
- [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md) - Azure Monitor는 데이터 및 구성 정보가 포함된 컨테이너인 Log Analytics 작업 영역에 로그 데이터를 저장합니다.
