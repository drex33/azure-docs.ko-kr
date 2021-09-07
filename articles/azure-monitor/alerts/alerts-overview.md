---
title: Azure의 경고 및 알림 모니터링 개요
description: Azure Monitor의 경고 개요
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ee0d6cd4c895bbcd78767776a86bd63cfa4f5242
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529227"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고 개요 

이 문서에서는 경고에 대해 설명하고 그 이점과 사용 방법을 소개합니다.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고란?

경고는 Azure Monitor에서 모니터링 데이터를 사용하여 인프라나 애플리케이션의 문제가 발견되는 경우에 사용자에게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 

## <a name="overview"></a>개요

아래 다이어그램은 경고의 흐름을 나타냅니다. 

![경고 흐름 다이어그램](media/alerts-overview/Azure-Monitor-Alerts.svg)

경고 규칙은 경고 및 경고가 발생할 때 수행되는 작업에서 분리됩니다. 경고 규칙은 경고의 대상 및 조건을 캡처합니다. 경고 규칙은 사용 또는 사용 안 함 상태입니다. 경고 규칙이 사용인 경우에만 경고가 발생합니다. 

다음은 경고 규칙의 주요 특성입니다.

**대상 리소스** - 경고에 사용할 수 있는 범위 및 신호를 정의합니다. 대상은 Azure 리소스일 수 있습니다. 예제 대상:

- 가상 머신
- 스토리지 계정
- Log Analytics 작업 영역.
- Application Insights 

특정 리소스(예: 가상 머신)의 경우 경고 규칙의 대상으로 여러 리소스를 지정할 수 있습니다.

**신호** - 대상 리소스에서 내보냅니다. 신호는 메트릭, 활동 로그, Application Insights 및 로그 유형일 수 있습니다.

**조건** - 대상 리소스에 적용되는 신호 및 논리의 조합입니다. 예제: 

- 백분율 CPU > 70%
- 서버 응답 시간 > 4 ms 
- 로그 쿼리의 결과 수 > 100

**경고 이름** – 사용자가 구성한 경고 규칙의 구체적인 이름입니다.

**경고 설명** – 사용자가 구성한 경고 규칙에 대한 설명입니다.

**심각도** - 경고 규칙에 지정된 조건이 충족되면 발생하는 경고의 심각도입니다. 심각도 범위는 0~4입니다.

- 심각도 0 = 위험
- 심각도 1 = 오류
- 심각도 2 - 경고
- 심각도 3 = 정보
- 심각도 4 = 자세한 정보 

**작업** - 경고가 발생할 때 수행되는 특정 작업입니다. 자세한 내용은 [작업 그룹](../alerts/action-groups.md)을 참조하세요.

## <a name="what-you-can-alert-on"></a>경고 가능한 내용

[데이터 원본 모니터링](./../agents/data-sources.md)에 설명된 대로 메트릭 및 로그에 대해 경고할 수 있습니다. 신호에 포함되는 사항들은 다음과 같으며 이에 국한되지 않습니다.

- 메트릭 값
- 로그 검색 쿼리
- 활동 로그 이벤트
- 기본 Azure 플랫폼의 상태
- 웹 사이트 가용성 테스트

## <a name="manage-alerts"></a>경고 관리

경고가 해결 과정에 있는지 지정하는 경고 상태를 설정할 수 있습니다. 경고 규칙에 지정된 조건이 충족되면 경고가 생성되거나 실행되고, 상태는 *새 경고* 입니다. 경고를 확인할 때 및 경고를 닫을 때 상태를 변경할 수 있습니다. 모든 상태 변경은 경고 기록에 저장됩니다.

다음은 지원되는 경고 상태입니다.

| 시스템 상태 | 설명 |
|:---|:---|
| 새로 만들기 | 문제를 감지했으며 아직 검토하지 않았습니다. |
| 승인됨 | 관리자가 경고를 검토하고 작업을 시작했습니다. |
| 종결 | 문제가 해결되었습니다. 경고가 닫힌 후 다른 상태로 변경하면 경고를 다시 열 수 있습니다. |

*경고 상태* 는 *모니터 조건* 과 다르며 독립적입니다. 경고 상태는 사용자가 설정합니다. 모니터 조건은 시스템에 의해 설정됩니다. 경고가 발생하면 경고의 모니터 조건이 '발생'으로 설정되고, 경고를 발생시킨 기본 조건이 지워지면 모니터 조건이 '해결됨'으로 설정됩니다. 

경고 상태는 사용자가 변경할 때까지 변경되지 않습니다. [경고 및 스마트 그룹의 상태를 변경하는 방법](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)을 알아보세요.

## <a name="alerts-experience"></a>경고 환경 
기본 경고 페이지는 특정 시간 범위 내에 생성된 경고의 요약 정보를 제공합니다. 심각도 상태별 총 경고 수를 나타내는 열을 통해 심각도별 총 경고 수가 표시됩니다. 아무 심각도나 선택하면 해당 심각도를 기준으로 필터링된 [모든 경고](#all-alerts-page) 페이지가 열립니다.

대신 [REST API를 사용하여 구독에서 생성된 경고 인스턴스를 프로그래밍 방식으로 열거](#manage-your-alert-instances-programmatically)할 수 있습니다.

> [!NOTE]
   >  지난 30일 동안 생성된 경고에만 액세스할 수 있습니다.

구독을 변경하거나 매개 변수를 필터링하여 페이지를 업데이트할 수 있습니다.

![경고 페이지 스크린샷](media/alerts-overview/alerts-page.png)

페이지 맨 위에 있는 드롭다운 메뉴에서 값을 선택하여 해당 보기를 필터링할 수 있습니다.

| 열 | 설명 |
|:---|:---|
| 구독 | 경고를 보려는 Azure 구독을 선택합니다. 필요에 따라 모든 구독을 선택할 수 있습니다. 선택한 구독에서 사용자가 액세스할 수 있는 경고만 보기에 포함됩니다. |
| 리소스 그룹 | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에 발생한 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

경고 페이지 맨 위에서 다음 값을 선택하면 다른 페이지가 열립니다.

| 값 | Description |
|:---|:---|
| 총 경고 수 | 선택한 조건과 일치하는 총 경고 수입니다. 이 값을 선택하면 필터 없이 [모든 경고] 보기가 열립니다. |
| 스마트 그룹 | 선택한 조건과 일치하는 경고에서 만들어진 스마트 그룹의 총수입니다. 이 값을 선택하면 [모든 경고] 보기에 스마트 그룹 목록이 열립니다.
| 총 경고 규칙 수 | 선택한 구독 및 리소스 그룹에 있는 총 경고 규칙 수입니다. 이 값을 선택하면 선택한 구독 및 리소스 그룹을 기준으로 필터링된 규칙 보기가 열립니다.


## <a name="manage-alert-rules"></a>경고 규칙 관리
**규칙** 페이지를 표시하려면 **경고 규칙 관리** 를 선택합니다. 규칙 페이지는 Azure 구독 전반의 모든 경고 규칙을 관리하는 단일 위치입니다. 모든 경고 규칙을 나열하며 대상 리소스, 리소스 그룹, 규칙 이름 또는 상태에 따라 정렬할 수 있습니다. 또한 이 페이지에서 경고 규칙을 편집, 사용 또는 사용하지 않도록 설정할 수 있습니다.  

 ![규칙 페이지 스크린샷](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>경고 규칙 만들기
모니터링 서비스 또는 신호 형식에 관계 없이 일관된 방식으로 경고 규칙을 작성할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
새 경고 규칙을 만드는 방법은 다음과 같습니다.
1. 경고의 _대상_ 을 선택합니다.
1. 대상에 사용 가능한 신호 중에서 _신호_ 를 선택합니다.
1. 신호의 데이터에 적용할 _논리_ 를 지정합니다.

이 간소화된 작성 프로세스에서는 Azure 리소스를 선택하기 전에 지원되는 모니터링 원본 또는 신호를 더 이상 알 필요가 없습니다. 사용 가능한 신호 목록은 선택한 대상 리소스를 기준으로 자동 필터링됩니다. 또한 해당 대상에 따라 경고 규칙의 논리를 정의하는 과정을 자동으로 안내합니다.  

경고 규칙을 만드는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../alerts/alerts-metric.md)를 참조하세요.

경고는 여러 Azure 모니터링 서비스 전체에서 제공됩니다. 각 서비스를 사용하는 방법 및 시기에 대한 자세한 내용은 [Azure 애플리케이션 및 리소스 모니터링](../overview.md)을 참조하세요. 


## <a name="all-alerts-page"></a>[모든 경고] 페이지 
**모든 경고** 페이지를 보려면 **총 경고 수** 를 선택합니다. 여기서 선택한 시간 내에 생성된 경고 목록을 볼 수 있습니다. 개별 경고 목록 또는 경고가 포함된 스마트 그룹 목록을 볼 수 있습니다. 페이지 맨 위에 있는 배너를 선택하면 보기가 전환됩니다.

![모든 경고 페이지 스크린샷](media/alerts-overview/all-alerts-page.png)

페이지 맨 위에 있는 드롭다운 메뉴에서 다음 값을 선택하여 보기를 필터링할 수 있습니다.

| 열 | 설명 |
|:---|:---|
| 구독 | 경고를 보려는 Azure 구독을 선택합니다. 필요에 따라 모든 구독을 선택할 수 있습니다. 선택한 구독에서 사용자가 액세스할 수 있는 경고만 보기에 포함됩니다. |
| 리소스 그룹 | 단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
| 리소스 종류 | 리소스 종류를 하나 이상 선택합니다. 선택한 형식의 대상이 있는 경고만 보기에 포함됩니다. 이 열은 리소스 그룹을 지정한 후에만 사용할 수 있습니다. |
| 리소스 | 리소스를 선택합니다. 해당 리소스가 대상으로 지정된 경고만 보기에 포함됩니다. 이 열은 리소스 종류를 지정한 후에만 사용할 수 있습니다. |
| 심각도 | 경고 심각도를 선택하거나 심각도에 상관없이 모든 경고를 포함하려면 **모두** 를 선택합니다. |
| 조건 모니터링 | 모니터 조건을 선택하거나, 모든 조건의 경고를 포함하려면 **모두** 를 선택합니다. |
| 경고 상태 | 경고 상태를 선택하거나, 모든 상태의 경고를 포함하려면 **모두** 를 선택합니다. |
| 서비스 모니터링 | 하나의 서비스를 선택하거나 모든 서비스를 포함하려면 **모두** 를 선택합니다. 서비스를 대상으로 사용하는 규칙에서 생성된 경고만 포함됩니다. |
| 시간 범위 | 선택한 시간 범위 내에 발생한 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

페이지 맨 위에서 **열** 을 선택하여 표시할 열을 선택합니다. 

## <a name="alert-details-page"></a>경고 정보 페이지
경고를 선택하면 이 페이지에서 경고에 대한 세부 정보를 제공하고 해당 상태를 변경할 수 있습니다.

![경고 정보 페이지 스크린샷](media/alerts-overview/alert-detail2.png)

경고 세부 정보 페이지에는 다음과 같은 섹션이 있습니다.

| 섹션 | Description |
|:---|:---|
| 요약 | 경고에 대한 속성과 기타 중요한 정보를 표시합니다. |
| 기록 | 경고에서 수행한 각 작업과 경고의 변경 내용을 나열합니다. 현재는 상태 변경으로 제한되어 있습니다. |
| 진단 | 경고가 포함된 스마트 그룹에 대한 정보입니다. *경고 수* 는 스마트 그룹에 포함된 경고 수를 나타냅니다. 경고 목록 페이지에서 시간 필터에 관계 없이 지난 30일 동안 생성된 동일한 스마트 그룹에 다른 경고를 포함합니다. 경고를 선택하면 세부 정보를 볼 수 있습니다. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>경고 인스턴스에 대한 Azure RBAC(역할 기반 액세스 제어)

경고 인스턴스의 사용 및 관리를 위해서는 사용자에게 [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 또는 [모니터링 읽기 권한자](../../role-based-access-control/built-in-roles.md#monitoring-reader)의 Azure 기본 제공 역할이 있어야 합니다. 이러한 역할은 구독 수준에서 리소스 수준의 세부적인 할당까지의 모든 Azure Resource Manager 범위에서 지원됩니다. 예를 들어 사용자에게 가상 머신 `ContosoVM1`에 대한 모니터링 기여자 액세스 권한만 있는 경우 해당 사용자는 `ContosoVM1`에서 생성된 경고만 사용하고 관리할 수 있습니다.

## <a name="manage-your-alert-instances-programmatically"></a>프로그래밍 방식으로 경고 인스턴스 관리

구독에 대해 생성된 경고를 프로그래밍 방식으로 쿼리하려고 할 수 있습니다. 쿼리는 Azure Portal 외부에서 사용자 지정 보기를 만들거나 패턴 및 추세를 식별하기 위해 경고를 분석하기 위한 것일 수 있습니다.

발생한 경고를 쿼리하려면 `AlertsManagementResources` 스키마에서 [Azure Resource Graph](../../governance/resource-graph/overview.md)를 사용하는 것이 좋습니다. 여러 구독에서 생성된 경고를 관리해야 하는 경우 Resource Graph가 권장됩니다.

Resource Graph REST API에 대한 다음 샘플 요청은 마지막 날에 하나의 구독 내에서 경고를 반환합니다.

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "alertsmanagementresources | where properties.essentials.lastModifiedDateTime > ago(1d) | project alertInstanceId = id, parentRuleId = tolower(tostring(properties['essentials']['alertRule'])), sourceId = properties['essentials']['sourceCreatedId'], alertName = name, severity = properties.essentials.severity, status = properties.essentials.monitorCondition, state = properties.essentials.alertState, affectedResource = properties.essentials.targetResourceName, monitorService = properties.essentials.monitorService, signalType = properties.essentials.signalType, firedTime = properties['essentials']['startDateTime'], lastModifiedDate = properties.essentials.lastModifiedDateTime, lastModifiedBy = properties.essentials.lastModifiedUserName"
}
```

Azure Resource Graph Explorer를 사용하여 Portal에서 이 Resource Graph 쿼리의 결과를 볼 수도 있습니다.[portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/alertsmanagementresources%0A%7C%20where%20properties.essentials.lastModifiedDateTime%20%3E%20ago(1d)%0A%7C%20project%20alertInstanceId%20%3D%20id%2C%20parentRuleId%20%3D%20tolower(tostring(properties%5B 'essentials'%5D%5B'alertRule'%5D))%2C%20sourceId%20%3D%20properties%5B'essentials'%5D%5B'sourceCreatedId'%5D%2C%20alertName%20%3D%20name%2C%20severity%20%3D%20properties.essentials.severity%2C%20status%20%3D%20properties.essentials.monitorCondition%2C%20state%20%3D%20properties.essentials.alertState%2C%20affectedResource%20%3D%20properties.essentials.targetResourceName%2C%20monitorService%20%3D%20properties.essentials.monitorService%2C%20signalType%20%3D%20properties.essentials.signalType%2C%20firedTime%20%3D%20properties%5B'essentials'%5D%5B'startDateTime'%5D%2C%20lastModifiedDate%20%3D%20properties.essentials.lastModifiedDateTime%2C%20lastModifiedBy%20%3D%20properties.essentials.lastModifiedUserName)

낮은 규모의 쿼리 시나리오에서 [경고 관리 REST API](/rest/api/monitor/alertsmanagement/alerts)를 사용하거나 발생된 경고를 업데이트할 수도 있습니다.

## <a name="smart-groups"></a>스마트 그룹

스마트 그룹은 경고 노이즈를 줄이고 문제 해결을 도와주는 기계 학습 알고리즘 기반의 경고 집계입니다. [스마트 그룹](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) 및 [스마트 그룹을 관리하는 방법](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)을 알아보세요.

## <a name="next-steps"></a>다음 단계

- [스마트 그룹에 대해 자세히 알아보기](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [작업 그룹](../alerts/action-groups.md)에 대해 자세히 알아보기
- [Azure에서 경고 인스턴스 관리](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [스마트 그룹 관리](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Azure 경고 가격 책정에 대해 자세히 알아보기](https://azure.microsoft.com/pricing/details/monitor/)
