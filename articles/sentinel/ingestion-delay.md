---
title: Microsoft 센티널에서 수집 지연 처리 | Microsoft Docs
description: Microsoft 센티널 예약 된 분석 규칙의 수집 지연 시간을 처리 합니다.
author: batamig
ms.topic: how-to
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 9f5a8e6b6a397d9b2bb0f0b2dd5d21aaff575b20
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218355"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>예약 된 분석 규칙의 수집 지연 처리

Microsoft 센티널은 [다양 한 원본](connect-data-sources.md)에서 데이터를 수집할 수 있지만, 각 데이터 원본에 대 한 수집 시간은 다양 한 상황에서 다를 수 있습니다.

이 문서에서는 지연 시간 수집을 수집 하는 방법을 설명 합니다.

## <a name="why-delay-is-significant"></a>지연이 중요 한 이유

예를 들어 사용자 지정 검색 규칙을 작성 하 고 마지막 필드에서 **쿼리 실행** 및 **조회 데이터** 를 5 분 마다 실행 하 여 마지막 5 분 동안의 데이터를 조회 하도록 설정 합니다.

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text="분석 규칙 마법사-새 규칙 만들기 창을 보여 주는 스크린샷" border="false":::

마지막 필드의 **조회 데이터** 는 *검색* 기간 이라고 하는 설정을 정의 합니다. 다음 다이어그램에 표시 된 것 처럼 지연이 없는 경우이 검색은 이벤트를 누락 하는 것이 가장 좋습니다.

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="5 분 모양 창을 보여 주는 다이어그램" border="false":::

이벤트는 생성 된 대로 도착 하 고 *lookback* 기간에 포함 됩니다.

이제 데이터 원본에 대해 약간의 지연이 있다고 가정 합니다. 이 예에서는 이벤트가 *생성* 된 후 2 분 *수집* 된 경우를 가정해 보겠습니다. 지연 시간은 2 분입니다.

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text="2 분의 지연 시간 동안 5 분 뒤로 보이는 창을 보여 주는 다이어그램" border="false":::

이 이벤트는 첫 번째 시작 기간 내에 생성 되지만 Microsoft 센티널 작업 영역에서 첫 번째 실행에서 수집 되지 않습니다. 다음에 예약 된 쿼리가 실행 될 때 이벤트를 수집 시간이 생성 된 필터는 5 분 이전에 발생 했기 때문에 이벤트를 제거 합니다. 이 경우 **규칙은 경고를 발생 시 지** 않습니다.

## <a name="how-to-handle-delay"></a>지연 처리 방법

이 문제를 해결 하려면 데이터 형식에 대 한 지연을 파악 해야 합니다. 이 예에서는 지연 시간을 2 분으로 이미 알고 있습니다. 

사용자 고유의 데이터를 위해 Kusto 함수를 사용 하는 지연을 이해 하 `ingestion_time()` 고 **timegenerated** 와 수집 시간 사이의 차이를 계산할 수 있습니다. 자세한 내용은 수집 [지연 계산](#calculate-ingestion-delay)을 참조 하세요.

지연을 확인 한 후 다음과 같이 문제를 해결할 수 있습니다.

- **검색 기간을 늘립니다**. 기본 intuition는 나중에 표시 되는 기간 크기를 늘릴 수 있음을 알려 줍니다. 이전 기간은 5 분 이며 지연 시간은 2 분 이므로이 *문제를 해결 하는* 데 도움이 됩니다. 예를 들어, 규칙 설정에서 다음을 수행 합니다.

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="이전 창에서 7 분으로 설정 하는 것을 보여 주는 스크린샷":::

    다음 다이어그램에서는 현재 표시 팩에 누락 된 이벤트를 포함 하는 방법을 보여 줍니다.

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="2 분의 지연 시간을 나타내는 7 분 분량의 창을 보여 주는 다이어그램입니다." border="false":::

- **중복 핸들** 입니다. 이제는 나중에 찾기 창이 겹치므로 중복 된 기간 동안만 중복을 만들 수 있습니다. 예를 들어 다른 이벤트는 다음 다이어그램에 표시 된 것 처럼 보일 수 있습니다.

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="겹치는 모양 창에서 중복을 만드는 방법을 보여 주는 다이어그램입니다." border="false":::

    이벤트 **Timegenerated** 값은 두 번 이상에 모두 있으므로 이벤트는 두 개의 경고를 발생 시킵니다. 중복을 해결 하는 방법을 찾아야 합니다.

- **이벤트를 특정 한 백업 기간에 연결** 합니다. 첫 번째 예제에서는 예약 된 쿼리가 실행 될 때 데이터가 수집 되지 않았기 때문에 이벤트를 누락 했습니다. 이벤트를 포함 하도록 조회를 확장 했지만이로 인해 중복이 발생 했습니다. 이벤트를 포함 하도록 확장 한 창에 연결 해야 합니다.

    원래 규칙 대신를 설정 하 여이 작업 `ingestion_time() > ago(5m)` 을 수행 `look-back = 5m` 합니다. 이 설정은 이벤트를 첫 번째 검색 창에 연결 합니다. 예:

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="이전 제한을 설정 하면 중복을 방지 하는 방법을 보여 주는 다이어그램" border="false":::

    이제 수집 시간 제한에는 나중에 추가 하는 2 분이 추가 됩니다. 첫 번째 예제에서는 이제 두 번째 실행 후 재생 기간이 이벤트를 캡처합니다.

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="이전 제한을 설정 하 여 이벤트를 캡처하는 방법을 보여 주는 다이어그램입니다." border="false":::

다음 샘플 쿼리는 수집 지연 문제를 해결 하기 위한 솔루션을 요약 합니다.

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>수집 지연 계산

기본적으로 Microsoft 센티널 예약 된 경고 규칙은 5 분의 이전 기간을 갖도록 구성 됩니다. 그러나 각 데이터 원본에는 자체의 개별 수집 지연이 있을 수 있습니다. 여러 데이터 형식을 조인 하는 경우에는 각 데이터 형식에 대 한 서로 다른 지연을 이해 하 여 나중에 조회 기간을 구성 해야 합니다.

Microsoft 센티널에 제공 되는 **작업 영역 사용 보고서** 에는 작업 영역으로 흐르는 여러 데이터 형식에 대 한 대기 시간 및 지연을 보여 주는 대시보드가 포함 되어 있습니다.

예:

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="테이블의 종단 간 대기 시간을 보여 주는 작업 영역 사용 보고서의 스크린샷":::


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
- [Azure Sentinel에서 경고 세부 정보 사용자 지정](customize-alert-details.md)
- [Azure 센티널에서 예약 된 분석 규칙에 대 한 템플릿 버전 관리](manage-analytics-rule-templates.md)
- [상태 모니터링 통합 문서 사용](monitor-data-connector-health.md)
- [Azure Monitor의 로그 데이터 수집 시간](/azure-monitor/logs/data-ingestion-time)
