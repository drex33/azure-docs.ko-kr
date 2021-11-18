---
title: Microsoft Sentinel | 수량자에서의 스트리밍 지연 처리 Microsoft Docs
description: Microsoft Sentinel 예약 분석 규칙에서의 스트리밍 지연을 처리합니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 2ce809d9121f97f885888f927a7f1dd2138f2816
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720894"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>예약된 분석 규칙에서의 스트리밍 지연 처리

Microsoft Sentinel은 [다양한 원본에서](connect-data-sources.md)데이터를 수집할 수 있지만 각 데이터 원본에 대한 수집 시간은 상황에 따라 다를 수 있습니다.

이 문서에서는 예약된 분석 규칙에 영향을 줄 수 있는 지연을 포함하는 방법과 이러한 차이를 해결하기 위해 이를 해결하는 방법을 설명합니다.

## <a name="why-delay-is-significant"></a>지연이 중요한 이유

예를 들어 사용자 지정 검색 규칙을 작성하고 마지막 필드의 **쿼리 실행** 및 **마지막 필드의 조회 데이터를** 5분마다 실행하도록 설정하여 지난 5분 동안의 데이터를 조회할 수 있습니다.

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text="분석 규칙 마법사 - 새 규칙 만들기 창을 보여주는 스크린샷." border="false":::

**마지막 필드의 조회 데이터는** *조회* 기간이라는 설정을 정의합니다. 이상적으로 지연이 없는 경우 다음 다이어그램에 표시된 것처럼 이 검색에 이벤트가 누락되지 않습니다.

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="5분 조회 창을 보여 주는 다이어그램." border="false":::

이벤트가 생성되면 *도착하며, 조회* 기간에 포함됩니다.

이제 데이터 원본에 약간의 지연이 있다고 가정합니다. 이 예제에서는 이벤트가 *생성된* 후 *2분* 후에 이벤트가 검색되었다고 가정해 보겠습니다. 지연은 2분입니다.

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text="지연이 2분인 5분 뒤로 창을 보여 주는 다이어그램." border="false":::

이벤트는 첫 번째 조회 기간 내에 생성되지만 첫 번째 실행 시 Microsoft Sentinel 작업 영역에서는 생성되지 않습니다. 예약된 쿼리가 다음에 실행될 때 이벤트를 검색하지만 시간 생성 필터는 5분 이상 전에 발생했기 때문에 이벤트를 제거합니다. 이 경우 **규칙은 경고를 발생시키지 않습니다.**

## <a name="how-to-handle-delay"></a>지연을 처리하는 방법

이 문제를 해결하려면 데이터 형식의 지연을 알아야 합니다. 이 예제에서는 지연이 2분이라는 것을 이미 알고 있습니다. 

사용자 고유의 데이터에 대해 Kusto 함수 사용 지연을 이해하고 `ingestion_time()` **TimeGenerated와** 수집 시간 간의 차이를 계산할 수 있습니다. 자세한 내용은 [Calculate ingestion delay을](#calculate-ingestion-delay)참조하세요.

지연을 결정한 후 다음과 같이 문제를 해결할 수 있습니다.

- **조회 기간을 늘입니다.** 기본 직관에서는 조회 기간 크기를 늘리면 도움이 됩니다. 조회 기간은 5분이고 지연은 2분이므로 조회 기간을 *7분으로* 설정하면 이 문제를 해결하는 데 도움이 됩니다. 예를 들어 규칙 설정에서 다음을 수행합니다.

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="조회 창을 7분으로 설정하는 방법을 보여 주는 스크린샷.":::

    다음 다이어그램에서는 이제 look-pack 기간에 누락된 이벤트가 어떻게 포함되는지 보여 주는 다이어그램입니다.

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="지연이 2분인 7분 뒤로 창을 보여 주는 다이어그램." border="false":::

- **중복을 처리합니다.** 이제 조회 창이 겹치므로 조회 기간을 늘리기만 하면 중복될 수 있습니다. 예를 들어 다른 이벤트는 다음 다이어그램과 같이 표시할 수 있습니다.

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="겹치는 조회 창에서 중복을 만드는 방법을 보여 주는 다이어그램" border="false":::

    **TimeGenerated** 이벤트 값은 두 가지 조회 기간에 모두 발견되므로 이벤트는 두 개의 경고를 발생합니다. 중복을 해결하는 방법을 찾아야 합니다.

- **이벤트를 특정 조회 기간 에 연결합니다.** 첫 번째 예제에서는 예약된 쿼리가 실행될 때 데이터가 수집되지 않았기 때문에 이벤트를 누락했습니다. 이벤트를 포함하도록 조회를 확장했지만 이로 인해 중복이 발생했습니다. 이벤트를 포함하도록 확장한 창에 연결해야 합니다.

    원래 규칙 대신 를 설정하여 이 작업을 `ingestion_time() > ago(5m)` `look-back = 5m` 수행합니다. 이 설정은 이벤트를 첫 번째 조회 창에 연결합니다. 예를 들어 다음과 같습니다.

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="ago 제한을 설정하여 중복을 방지하는 방법을 보여 주는 다이어그램" border="false":::

    이제 다시 조회 기간에 추가한 추가 2분을 2분 더 줄입니다. 그리고 첫 번째 예제의 경우 두 번째 실행 조회 기간은 이제 이벤트를 캡처합니다.

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="ago 제한을 설정하여 이벤트를 캡처하는 방법을 보여 주는 다이어그램" border="false":::

다음 샘플 쿼리는 검색 지연 문제를 해결하기 위한 솔루션을 요약합니다.

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>추출 지연 계산

기본적으로 Microsoft Sentinel 예약 경고 규칙은 5분 조회 기간을 갖도록 구성됩니다. 그러나 각 데이터 원본에는 고유한 개별 수집 지연이 있을 수 있습니다. 여러 데이터 형식을 조인하는 경우 조회 기간을 올바르게 구성하려면 각 데이터 형식에 대한 다양한 지연을 이해해야 합니다.

Microsoft Sentinel에서 제공하는 **작업 영역 사용 현황 보고서에는** 작업 영역으로 흐르는 다양한 데이터 형식에 대한 대기 시간 및 지연을 보여 주는 대시보드가 포함되어 있습니다.

예를 들어 다음과 같습니다.

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="테이블별 종단 간 대기 시간을 보여주는 작업 영역 사용량 보고서의 스크린샷":::


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
- [Azure Sentinel에서 경고 세부 정보 사용자 지정](customize-alert-details.md)
- [Azure Sentinel 예약된 분석 규칙에 대한 템플릿 버전 관리](manage-analytics-rule-templates.md)