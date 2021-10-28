---
title: 프로비전이 Azure Active Directory의 Azure Monitor 로그와 통합되는 방식을 이해합니다.
description: 프로비전이 Azure Active Directory의 Azure Monitor 로그와 통합되는 방식을 이해합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 918267b9dc2d7d8548d5406d590a03db13870749
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991834"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>프로비전이 Azure Monitor 로그와 통합되는 방식 이해

프로비전은 Azure Monitor 로그 및 Log Analytics와 통합됩니다. Azure 모니터링을 사용하면 통합 문서(다른 이름: 대시보드) 만들기, 30일이 넘는 프로비전 로그 저장, 사용자 지정 쿼리 및 경고 만들기 등의 작업을 수행할 수 있습니다. 이 문서에서는 프로비전 로그가 Azure Monitor 로그와 통합되는 방법을 설명합니다. 프로비전 로그가 일반적으로 작동하는 방식에 대한 자세한 내용은 [프로비전 로그](../reports-monitoring/concept-provisioning-logs.md)를 참조하세요.

## <a name="enabling-provisioning-logs"></a>프로비전 로그를 사용하도록 설정

Azure 모니터링 및 Log Analytics에 대해 이미 익숙해야 합니다. 그렇지 않은 경우 해당 항목에 대해 알아본 다음, 다시 돌아와서 애플리케이션 프로비전 로그에 대해 자세히 알아보세요. Azure 모니터링에 대해 자세히 알아보려면 [Azure Monitor 개요](../../azure-monitor/overview.md)를 참조하세요. Azure Monitor 로그 및 Log Analytics에 대해 자세히 알아보려면 [Azure Monitor의 로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

Azure 모니터링을 구성한 후에는 애플리케이션 프로비전에 대해 로그를 사용하도록 설정할 수 있습니다. 옵션은 **진단 설정** 페이지에 있습니다.

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="애플리케이션 프로비전 로그 사용" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 작업 영역을 최근에 프로비전한 경우에는 로그를 보내기까지 시간이 걸릴 수 있습니다. 구독이 *microsoft.insights* 를 사용하도록 등록되지 않았다는 오류가 표시되면 몇 분 후에 다시 확인하세요.
 
## <a name="understanding-the-data"></a>데이터 이해
프로비전에서 로그 뷰어에게 보내는 기본 데이터 스트림은 거의 동일합니다. Azure Monitor 로그는 Azure Portal UI 및 Azure API와 거의 동일한 스트림을 가져옵니다. 로그 필드에는 몇 가지 **차이점** 만 있으며, 다음 표에 설명되어 있습니다. 이러한 필드에 대해 자세히 알아보려면 [provisioningObjectSummary 나열](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta)을 참조하세요.

|Azure Monitor 로그   |Azure Portal UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|상태 |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor 통합 문서

Azure Monitor 통합 문서는 데이터 분석을 위한 유연한 캔버스를 제공합니다. Azure Portal 내에서 풍부한 시각적 보고서를 만들 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요](../../azure-monitor/visualize/workbooks-overview.md)를 참조하세요.

애플리케이션 프로비전은 미리 작성된 통합 문서 집합과 함께 제공됩니다. 이러한 문서는 통합 문서 페이지에서 찾을 수 있습니다. 데이터를 보려면 모든 필터(timeRange, jobID, appName)가 채워져 있는지 확인해야 합니다. 또한 앱을 프로비전했는지도 확인해야 합니다. 그렇지 않으면 로그에 데이터가 없습니다.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="애플리케이션 프로비전 통합 문서" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="애플리케이션 프로비전 대시보드" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>사용자 지정 쿼리

사용자 지정 쿼리를 만들고 Azure 대시보드에 데이터를 표시할 수 있습니다. 자세한 내용은 [Log Analytics 데이터의 대시보드 만들기 및 공유](../../azure-monitor/logs/get-started-queries.md)를 참조하세요. 또한 [Azure Monitor의 로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 확인하세요.

다음은 애플리케이션 프로비전을 시작하는 몇 가지 샘플입니다.

원본 시스템에서 사용자의 ID를 기반으로 사용자에 대한 로그를 쿼리합니다.
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

ErrorCode당 개수 요약:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

작업별 일일 이벤트 수를 요약합니다.
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

이벤트 및 프로젝트 키 속성 100개를 가져옵니다.
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>사용자 지정 경고

Azure Monitor를 사용하면 프로비전과 관련된 주요 이벤트에 대한 알림을 받을 수 있도록 사용자 지정 경고를 구성할 수 있습니다. 예를 들어 오류 급증에 대한 경고를 받을 수 있습니다. 또는 비활성화 또는 삭제 급증에 대해서도 가능합니다. 경고를 받을 수 있는 또 다른 예는 프로비전 부족입니다. 이런 경우 뭔가 잘못되었음을 나타냅니다.

경고에 대한 자세한 내용은 [Azure Monitor 로그 경고](../../azure-monitor/alerts/alerts-log.md)를 참조하세요.

오류가 급증하면 경고합니다. jobID를 애플리케이션의 jobID로 바꿉니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="오류가 급증하면 경고합니다." lightbox="media/application-provisioning-log-analytics/alert1.png":::

프로비전 서비스 실행을 중지시키는 문제가 있을 수 있습니다. 지정된 시간 간격 동안 프로비전 이벤트가 없는 경우를 감지하려면 다음 경고를 사용합니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="프로비전 서비스 실행을 중지시키는 문제가 있을 수 있습니다." lightbox="media/application-provisioning-log-analytics/alert2.png":::

비활성화 또는 삭제가 급증하면 경고합니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="비활성화 또는 삭제가 급증하면 경고합니다." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>커뮤니티 기여

애플리케이션 프로비전 쿼리 및 대시보드에 대한 오픈 소스 및 커뮤니티 기반 접근 방식을 사용하고 있습니다. 다른 사람에게 유용할 수 있다고 생각하는 쿼리, 경고 또는 통합 문서를 빌드한 경우에는 [AzureMonitorCommunity GitHub 리포지토리](https://github.com/microsoft/AzureMonitorCommunity)에 게시해주세요. 그런 다음, 링크가 포함된 이메일을 보내주세요. 다른 사람들도 활용할 수 있도록 검토하고 서비스에 게시하겠습니다. provisioningfeedback@microsoft.com으로 문의할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [로그 분석](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Monitor 로그에서 쿼리 시작](../../azure-monitor/logs/get-started-queries.md)
- [Azure Portal에서 경고 그룹 만들기 및 관리](../../azure-monitor/alerts/action-groups.md)
- [Azure Active Directory용 로그 분석 보기 설치 및 사용](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [프로비전 로그 API](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)