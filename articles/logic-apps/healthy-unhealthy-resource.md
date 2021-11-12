---
title: Azure Security Center에서 논리 앱을 모니터링하기 위한 로깅 설정
description: 진단 로깅을 설정하여 Azure Security Center에서 Logic Apps 리소스의 상태를 모니터링합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: aafa0584c03c2e7152ab13c5d2a89eb504ac9a14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337153"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 논리 앱을 모니터링 하기 위한 로깅 설정

[Microsoft Azure Security Center](../security-center/security-center-introduction.md)에서 Logic Apps 리소스를 모니터링하는 경우 [논리 앱이 기본 정책을 준수하는지 여부를 검토](#view-logic-apps-health-status)할 수 있습니다. 로깅을 사용하도록 설정하고 로그의 대상을 올바르게 설정하고 나면 Azure에서 Logic Apps 리소스의 상태를 표시합니다. 이 문서에서는 진단 로깅을 구성하고 모든 논리 앱이 정상 리소스인지 확인하는 방법을 설명합니다.

> [!TIP]
> Logic Apps 서비스에 대한 현재 상태를 확인하려면 [Azure 상태 페이지](https://status.azure.com/)를 검토합니다. 이 페이지에는 사용 가능한 각 지역의 다른 제품 및 서비스에 대한 상태가 나열됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [진단 로깅을 사용](#enable-diagnostic-logging)하는 기존 논리 앱
* 논리 앱에 대한 로깅을 사용하도록 설정하는 데 필요한 Log Analytics 작업 영역입니다. 작업 영역이 없으면 먼저 [작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 선택합니다.

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용

논리 앱에 대한 리소스 상태를 보려면 먼저 [진단 로깅을 설정](monitor-logic-apps-log-analytics.md)해야 합니다. 이미 Log Analytics 작업 영역이 있는 경우 논리 앱을 만들 때 또는 기존 논리 앱에서 로깅을 사용하도록 설정할 수 있습니다.

> [!TIP]
> 기본적으로 Logic Apps에 대한 진단 로그를 사용하도록 설정하는 것이 좋습니다. 그러나 논리 앱에 대해 이 설정을 제어합니다. 논리 앱에 대한 진단 로그를 사용하도록 설정하는 경우 이 정보를 사용하여 보안 인시던트를 분석할 수 있습니다.

### <a name="check-diagnostic-logging-setting"></a>진단 로깅 설정 확인

논리 앱에서 진단 로깅을 사용 하도록 설정 했는지 확실 하지 않은 경우 클라우드에 대해 Defender를 체크 인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **클라우드 용 Defender** 를 입력 하 고 선택 합니다.
1. 작업 보호 대시보드 메뉴의 **일반** 에서 **권장 사항** 를 선택 합니다.
1. 보안 제안 사항 표의 보안 컨트롤 표에서 **감사 및 로깅 사용** &gt; **Logic Apps의 진단 로그를 사용하도록 설정해야 함** 을 찾아 선택합니다.
1. 권장 사항 페이지에서 **수정 단계** 섹션을 확장하고 옵션을 검토합니다. **빠른 수정!** 단추를 선택하거나 수동 수정 지침에 따라 Logic Apps 진단을 사용하도록 설정할 수 있습니다.

## <a name="view-logic-apps-health-status"></a>논리 앱의 상태 보기

[진단 로깅을 사용 하도록 설정](#enable-diagnostic-logging)하 고 나면 클라우드의 Defender에서 논리 앱의 상태를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **클라우드 용 Defender** 를 입력 하 고 선택 합니다.
1. 작업 보호 대시보드 메뉴의 **일반** 에서 **인벤토리** 를 선택 합니다.
1. 인벤토리 페이지에서 자산 목록을 필터링하여 Logic Apps 리소스만 표시합니다. 페이지 메뉴에서 **리소스 종류** &gt; **논리 앱** 을 선택합니다.

   **비정상 리소스** 카운터는 클라우드에서 Defender가 비정상으로 간주 하는 논리 앱의 수를 표시 합니다.
1.  논리 앱 리소스 목록에서 **권장 사항** 열을 검토합니다. 특정 논리 앱에 대한 상태 세부 정보를 검토하려면 리소스 이름을 선택하거나 줄임표 단추( **...** ) &gt; **리소스 보기** 를 선택합니다.
1.  잠재적 리소스 상태 문제를 수정하려면 논리 앱에 대해 나열된 단계를 따르세요.

진단 로깅을 이미 사용하는 경우 로그의 대상에 문제가 있을 수 있습니다. [다른 진단 로깅 대상의 문제를 해결하는 방법](#fix-diagnostic-logging-for-logic-apps)을 검토합니다.

## <a name="fix-diagnostic-logging-for-logic-apps"></a>논리 앱에 대한 진단 로깅 수정

[논리 앱이 클라우드에 대해 Defender에서 비정상으로 표시 되는](#view-logic-apps-health-status)경우 Azure Portal 또는 Azure CLI를 통해 코드 보기에서 논리 앱을 엽니다. 그런 다음 진단 로그에 대한 대상 구성 즉, [Azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations) 또는 [Azure Storage 계정](#storage-account-destination)을 확인합니다.

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics 및 Event Hubs 대상

Log Analytics 또는 Event Hubs를 Logic Apps 진단 로그의 대상으로 사용하는 경우 다음 설정을 확인합니다. 

1. 진단 로그를 사용하도록 설정했는지 확인하려면 진단 설정 `logs.enabled` 필드가 `true`로 설정되었는지 확인합니다. 
1. 대신 스토리지 계정을 대상으로 설정하지 않았는지 확인하려면 `storageAccountId` 필드가 `false`로 설정되었는지 확인합니다.

예를 들면 다음과 같습니다.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>스토리지 계정 대상

스토리지 계정을 Logic Apps 진단 로그의 대상으로 사용하는 경우 다음 설정을 확인합니다.

1. 진단 로그를 사용하도록 설정했는지 확인하려면 진단 설정 `logs.enabled` 필드가 `true`로 설정되었는지 확인합니다.
1. 진단 로그에 대한 보존 정책을 사용하도록 설정했는지 확인하려면 `retentionPolicy.enabled` 필드가 `true`로 설정되었는지 확인합니다.
1. 보존 시간을 0~365일로 설정했는지 확인하려면 `retentionPolicy.days` 필드가 0과 365 사이의 숫자로 설정되었는지 확인합니다.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```
