---
title: Azure Sentinel 인시던트 메트릭을 사용한 SOC관리 | Microsoft Docs
description: Azure Sentinel 인시던트 메트릭 화면 및 통합 문서를 참고하여 SOC(보안 운영 센터)를 관리할 수 있습니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2021
ms.author: yelevin
ms.openlocfilehash: 0f6fc1ef284cc1e9ac984af0d5b3ace2314c1afa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023104"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>인시던트 메트릭을 사용하여 SOC를 효율적으로 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

SOC(보안 운영 센터) 관리자는 팀의 성과를 측정하기 위해 전체 효율성 메트릭과 측정값을 손쉽게 유지할 수 있어야 합니다. 심각도, MITRE 전술, 평균 심사 시간, 평균 해결 시간 등과 같은 다양한 기준으로 시간에 따른 인시던트 작업을 확인해야 할 것입니다. 이제 Azure Sentinel Log Analytics의 새 **SecurityIncident** 테이블 및 스키마와 함께 제공되는 **보안 작업 효율성** 통합 문서를 통해 이러한 데이터를 사용할 수 있습니다. 시간에 따른 팀의 성과를 시각화하고 이 인사이트를 사용하여 효율성을 향상시킬 수 있습니다. 인시던트 테이블에 대해 고유한 KQL 쿼리를 작성 및 사용하여 특정 감사 요구 사항 및 KPI에 맞는 사용자 지정 통합 문서를 만들 수도 있습니다.

## <a name="use-the-security-incidents-table"></a>보안 인시던트 테이블 사용

**SecurityIncident** 테이블은 Azure Sentinel에 기본 제공됩니다. 이 테이블은 **로그** 아래에 있는 **SecurityInsights** 컬렉션의 다른 테이블과 함께 찾을 수 있습니다. Log Analytics의 다른 테이블처럼 쿼리할 수 있습니다.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="보안 인시던트 테이블":::

인시던트를 생성 또는 업데이트할 때마다 테이블에 새 로그 항목이 추가됩니다. 이렇게 하면 인시던트 변경 내용을 추적하고 훨씬 더 강력한 SOC 메트릭을 사용할 수 있습니다. 하지만 인시던트에 대한 중복 항목을 제거해야 할 수도 있으므로(실행 중인 정확한 쿼리에 따라 달라짐), 이 테이블에 대한 쿼리를 생성할 때 이 점을 염두에 두어야 합니다. 

예를 들어, 인시던트 번호로 정렬된 모든 인시던트 목록을 반환하려고 할 때 인시던트당 가장 최근 로그만 반환하려는 경우, 다음 `arg_max()` [집계 함수](/azure/data-explorer/kusto/query/arg-max-aggfunction)와 함께 KQL [summarize 연산자를](/azure/data-explorer/kusto/query/summarizeoperator) 사용하여 이 작업을 수행할 수 있습니다.

```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>추가 샘플 쿼리

인시던트 상태 - 지정된 시간 프레임의 상태 및 심각도별 모든 인시던트:

```Kusto
let startTime = ago(14d);
let endTime = now();
SecurityIncident
| where TimeGenerated >= startTime
| summarize arg_max(TimeGenerated, *) by IncidentNumber
| where LastModifiedTime  between (startTime .. endTime)
| where Status in  ('New', 'Active', 'Closed')
| where Severity in ('High','Medium','Low', 'Informational')
```

평균 종료 시간:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

평균 심사 시간:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>보안 작업 효율성 통합 문서

**SecurityIncidents** 테이블을 보완 하기 위해, SOC 작업을 모니터링 하는 데 사용할 수 있는 뛰어난 **보안 작업 효율성** 통합 문서 템플릿을 제공 했습니다. 통합 문서에는 다음 메트릭이 포함되어 있습니다. 
- 시간에 따라 생성된 인시던트 
- 종료 분류, 심각도, 소유자, 상태별로 생성된 인시던트 
- 평균 심사 시간 
- 평균 종료 시간 
- 시간에 따라 심각도, 소유자, 상태, 제품, 전략별로 생성된 인시던트 
- 심사 시간 백분위 
- 종료 시간 백분위 
- 소유자 당 평균 심사 시간 
- 최근 활동 
- 최근 종료 분류  

Azure Sentinel 탐색 메뉴에서 **통합 문서** 를 선택하고, **템플릿** 탭을 선택하여 새 통합 문서 템플릿을 찾을 수 있습니다. 갤러리에서 **보안 작업 효율성** 을 선택하고, **저장된 통합 문서 보기** 및 **템플릿 보기** 단추 중 하나를 클릭합니다.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="보안 인시던트 통합 문서 갤러리":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="보안 인시던트 통합 문서 완료":::

템플릿을 사용하여 특정 요구 사항에 맞게 사용자 고유의 사용자 지정 통합 문서를 만들 수 있습니다.

## <a name="securityincidents-schema"></a>SecurityIncidents 스키마

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>다음 단계

- Azure Sentinel을 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- [Azure Sentinel에 데이터를 등록](quickstart-onboard.md)하고 [데이터 및 잠재적 위협을 표시](get-visibility.md)하는 방법에 대해 알아봅니다.
