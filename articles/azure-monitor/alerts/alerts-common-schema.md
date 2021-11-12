---
title: Azure Monitor 경고에 대한 일반 경고 스키마
description: 일반 경고 스키마 개념, 사용해야 하는 이유 및 사용하도록 설정하는 방법 이해
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 60f3017312c8d650085d395e08d92af90ebf3f8d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327408"
---
# <a name="common-alert-schema"></a>일반 경고 스키마

이 문서에서는 일반적인 경고 스키마의 정의, 사용의 이점 및 사용 하도록 설정 하는 방법을 설명 합니다.

## <a name="what-is-the-common-alert-schema"></a>일반 경고 스키마란?

일반 경고 스키마는 현재 Azure에서 경고 알림에 대한 소비 환경을 표준화합니다. 지금까지 Azure의 세 가지 경고 유형(메트릭, 로그 및 활동 로그)에는 자체 이메일 템플릿, 웹후크 스키마 등이 있었습니다. 일반 경고 스키마를 사용하면 일관된 스키마로 경고 알림을 받을 수 있습니다.

경고 인스턴스는 **영향을 받은 리소스** 및 **경고의 원인** 을 설명하며, 이러한 인스턴스는 다음 섹션의 일반 스키마에 설명되어 있습니다.

- **필수**: 모든 경고 유형에 공통인 **표준화된 필드** 세트로, 경고가 발생하는 **리소스** 를 설명하고 추가 일반 경고 메타데이터(예: 심각도 또는 설명)를 설명합니다.
- **경고 컨텍스트**: 경고 **유형에 따라** 달라 지는 필드를 사용 하 여 **경고의 원인을** 설명 하는 필드 집합입니다. 예를 들어 메트릭 경고에는 경고 컨텍스트에 메트릭 이름 및 메트릭 값과 같은 필드가 있는 반면, 활동 로그 경고에는 경고를 생성한 이벤트에 대한 정보가 있습니다.

고객으로부터 듣는 일반적인 통합 시나리오에는 피벗(예: 리소스 그룹)을 기반으로 경고 인스턴스를 관련 팀에 회람하는 작업이 포함됩니다. 그러면 담당 팀이 작업을 시작합니다. 일반 경고 스키마에서는 필수 필드를 활용하여 경고 유형 전반에 표준화된 회람 논리를 적용하고, 컨텍스트 필드는 관련 팀에서 추가 조사가 가능하도록 그대로 둘 수 있습니다.

따라서 잠재적으로 통합의 수를 적게 유지할 수 있기 때문에 관리 및 유지 관리 프로세스를 훨씬 더 간단한 작업으로 만들 수 있습니다. 또한 이후 경고 페이로드 보강(예: 사용자 지정, 진단 보강 등)은 일반 스키마에만 노출됩니다.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>일반 경고 스키마는 어떤 기능을 향상시키나요?

일반 경고 스키마는 주로 경고 알림에 나타납니다. 볼 수 있는 개선 사항은 다음과 같습니다.

| 작업 | 개선 사항|
|:---|:---|
| 메일 | 일관되고 상세한 이메일 템플릿으로 문제를 한 눈에 쉽게 진단할 수 있습니다. 포털의 경고 인스턴스 및 영향을 받는 리소스에 대한 딥 링크가 포함되어 있어서 신속하게 수정 프로세스로 이동할 수 있습니다. |
| Webhooks/Logic Apps/Azure Function/Automation Runbook | 모든 경고 유형에 대한 일관된 JSON 구조를 통해 다양한 경고 유형 간에 쉽게 통합을 구축할 수 있습니다. |

또한 새 스키마를 사용하면 Azure Portal 및 Azure 모바일 앱 모두에 대해 더 풍부한 경고 사용 환경을 구현할 수 있습니다.

[Webhooks/Logic Apps/Azure Functions/Automation Runbook의 스키마 정의에 대해 자세히 알아보세요.](./alerts-common-schema-definitions.md)

> [!NOTE]
> 다음 작업은 일반 경고 스키마를 지원하지 않습니다. ITSM 커넥터.

## <a name="how-do-i-enable-the-common-alert-schema"></a>어떻게 일반 경고 스키마를 사용하도록 설정할 수 있나요?

포털과 REST API 모두에서 작업 그룹을 통해 일반 경고 스키마를 옵트인하거나 옵트아웃할 수 있습니다. 새 스키마로 전환하는 토글은 작업 수준에 있습니다. 예를 들어 이메일 작업과 웹후크 작업을 개별적으로 옵트인해야 합니다.

> [!NOTE]
> 1. 다음 경고 유형은 기본적으로 공통 스키마를 지원 합니다 (옵트인 필요 없음).
>     - 스마트 감지 경고
> 1. 다음 경고 유형은 현재 일반 스키마를 지원하지 않습니다.
>     - [VM 인사이트](../vm/vminsights-overview.md)에서 생성된 경고
>     - [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md)에서 생성된 경고

### <a name="through-the-azure-portal"></a>Azure Portal을 통해

![일반 경고 스키마 옵트인](media/alerts-common-schema/portal-opt-in.png)

1. 작업 그룹에서 기존 작업이나 새 작업을 엽니다.
1. 일반 경고 스키마를 사용하도록 설정하려면 보이는 것처럼 토글에 대해 '예'를 선택합니다.

### <a name="through-the-action-groups-rest-api"></a>작업 그룹 REST API를 통해

[작업 그룹 API](/rest/api/monitor/actiongroups)를 사용하여 일반 경고 스키마를 옵트인할 수도 있습니다. REST API 호출을 [생성 또는 업데이트](/rest/api/monitor/actiongroups/createorupdate)하는 동안 이메일/웹후크/논리 앱/Azure Function/Automation Runbook 작업에 대해 "useCommonAlertSchema" 플래그를 'true'(옵트인) 또는 'false'(옵트아웃)로 설정할 수 있습니다.

예를 들어 REST API [생성 또는 업데이트](/rest/api/monitor/actiongroups/createorupdate)에 대한 아래 요청 본문은 다음과 같은 작업을 수행합니다.

- 이메일 작업 "John Doe's email"에 대한 일반 경고 스키마를 사용하도록 설정
- 이메일 작업 "Jane Smith's email"에 대한 일반 경고 스키마를 사용하지 않도록 설정
- 웹후크 작업 "Sample webhook"에 대한 일반 경고 스키마를 사용하도록 설정

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```

## <a name="next-steps"></a>다음 단계

- [Webhooks/Logic Apps/Azure Functions/Automation Runbook에 대한 일반 경고 스키마 정의](./alerts-common-schema-definitions.md)
- [일반 경고 스키마를 활용하여 모든 경고를 처리하는 논리 앱을 만드는 방법을 알아봅니다.](./alerts-common-schema-integrations.md)
