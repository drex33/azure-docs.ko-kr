---
title: Application Insights 사용 영향 - Azure Monitor
description: 여러 속성이 앱 일부의 전환율에 미칠 수 있는 영향을 분석합니다.
ms.topic: conceptual
author: mattmccleary
ms.author: mmcc
ms.date: 07/30/2021
ms.openlocfilehash: 0d09f93c5a0a541929169130feb921a8e65144be
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133237"
---
# <a name="impact-analysis-with-application-insights"></a>Application Insights를 사용하여 영향 분석

Impact는 로드 시간 및 기타 속성이 앱의 여러 부분에서 전환율에 미치는 영향을 분석합니다. 정확도를 높이기 위해 **페이지 보기**, **사용자 지정 이벤트** 또는 **요청** 의 **모든 차원** 이 여러 **페이지 보기** 또는 **사용자 지정 이벤트** 에 미치는 영향을 탐색합니다. 

## <a name="still-not-sure-what-impact-does"></a>Impact가 무엇인지 잘 모르겠나요?

Impact는 사이트의 느린 속도가 사용자의 관심도 유지에 미치는 영향에 대한 팀원과의 논쟁을 일단락할 수 있는 궁극의 도구입니다. 사용자들이 약간의 속도 저하는 허용하겠지만, Impact는 최적화와 성능의 균형을 어떻게 맞춰야 사용자 전환율을 최대화할 수 있는지에 대한 정보를 제공합니다.

하지만 성능 분석은 Impact의 기능 중 극히 일부에 불과합니다. Impact는 사용자 지정 이벤트 및 차원을 지원하므로, 클릭 몇 번이면 사용자 브라우저 선택이 다양한 전환율과 어떤 관계가 있는지 상관 관계를 살펴볼 수 있습니다.

> [!NOTE]
> Application Insights 리소스는 영향 분석 통합 문서를 사용하기 위한 페이지 보기 또는 사용자 지정 이벤트를 포함해야 합니다. [Application Insights JavaScript SDK를 사용하여 자동으로 페이지 뷰를 수집하도록 앱을 설정하는 방법에 대해 알아봅니다](./javascript.md). 또한 상관 관계를 분석하는 것이므로 샘플 크기가 중요합니다.

## <a name="impact-analytics-workbook"></a>영향 분석 통합 문서 

영향 분석 통합 문서를 사용하려면 Application Insights 리소스에서 **사용** > **영향** 으로 이동하고 **영향 분석 통합 문서** 를 선택합니다. 또는 **통합 문서** 탭에서 **공개 템플릿** 을 선택한 다음 *사용* 에서 **사용자 영향 분석** 을 선택합니다.

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="공개 템플릿의 통합 문서 갤러리 스크린샷." lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>통합 문서 사용

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="초기 페이지 보기, 사용자 지정 이벤트 또는 요청을 선택할 수 있는 위치를 보여 주는 스크린샷입니다." lightbox="./media/usage-impact/selected-event.png":::

1. **선택한 이벤트** 드롭다운에서 이벤트를 선택합니다.
2. **분석 대상** 드롭다운에서 메트릭을 선택합니다.
3. **영향을 미치는 이벤트** 드롭다운에서 이벤트를 선택합니다. 
1. 필터를 추가하려면 **선택한 이벤트 필터 추가** 및/또는 **영향을 미치는 이벤트 필터 추가** 에서 추가합니다.


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>페이지 로드 시간이 내 페이지에서 전환하는 사람들의 수에 영향을 주나요?

영향 통합 문서로 질문에 대답하기 전에, 초기 페이지 보기, 사용자 지정 이벤트 또는 요청을 선택합니다.

1. **선택한 이벤트** 드롭다운에서 이벤트를 선택합니다.
2. **기간** 의 기본 섹션에서 **분석 대상** 은 그대로 둡니다(이 컨텍스트에서 **기간** 은 **페이지 로드 시간** 의 별칭).
3. **영향을 미치는 이벤트** 드롭다운에서 사용자 지정 이벤트를 선택합니다. 이 이벤트는 1단계에서 선택한 페이지 보기의 UI 요소와 일치해야 합니다.


:::image type="content" source="./media/usage-impact/impact.png" alt-text="선택한 이벤트가 기간별로 분석된 홈페이지의 예를 보여 주는 스크린샷." lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>사용자 지정 방식으로 페이지 보기 또는 로드 시간을 추적하면 어떻게 되나요?

Impact는 표준 및 사용자 지정 속성/측정을 모두 지원합니다. 원하는 방법을 사용하시면 됩니다. 기간 대신, 기본 및 보조 이벤트에서 필터를 사용하면 좀 더 구체적인 정보를 얻을 수 있습니다.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>국가 또는 지역에 따라 사용자의 전환율이 서로 다른가요?

1. **선택한 이벤트** 드롭다운에서 이벤트를 선택합니다.
2. **분석 대상** 드롭다운에서 "국가 또는 지역"을 선택합니다.
3. **영향을 미치는 이벤트** 드롭다운에서는 1단계에서 선택한 페이지 보기의 UI 요소와 일치하는 사용자 지정 이벤트를 선택합니다.

:::image type="content" source="./media/usage-impact/regions.png" alt-text="국가 및 지역별로 분석된 GET으로 선택된 이벤트의 예를 보여 주는 스크린샷." lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>영향 분석 통합 문서는 이러한 전환율을 어떻게 계산하나요?

내부적으로 영향 분석 통합 문서는 [피어슨 상관 계수](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)에 따라 다릅니다. 결과는 -1부터 1 사이에서 계산되며, -1은 음의 선형 상관 관계를 나타내고 1은 양의 선형 상관 관계를 나타냅니다.

Impact Analysis의 기본적인 작동 원리는 다음과 같습니다.

_A_ = 첫 번째 드롭다운 목록에서 선택하는 기본 페이지 보기/사용자 지정 이벤트/요청. (**선택한 이벤트**).

_B_ = 선택하는 보조 페이지 보기/사용자 지정 이벤트(**이 다음의 사용량에 미치는 영향을 분석합니다.** ).

Impact는 선택한 시간 범위에서 사용자의 모든 세션의 예제를 살펴봅니다. 각 세션에서, _A_ 의 발생을 찾습니다.

그 후 다음과 같은 두 조건 중 하나에 따라 세션이 두 종류의 _하위 세션_ 으로 나뉩니다.

- 전환된 하위 세션은 _B_ 이벤트로 끝나는 한 세션을 구성하고 _B_ 이전에 발생하는 모든 _A_ 이벤트를 포함합니다.
- 전환되지 않은 하위 세션은 터미널 _B_ 없이 모든 _A_ 가 발생할 때 발생합니다.

Impact의 궁극적인 계산 방법은 분석 기준이 메트릭인지 아니면 차원인지에 따라 다릅니다. 메트릭인 경우 하위 세션에 있는 모든 _A_ 를 평균합니다. 차원인 경우 각 _A_ 의 값으로 _B_ 에 할당된 값을 _1/N_ 등분하며, 여기서 _N_ 은 하위 세션에 있는 _A_ 의 개수입니다.

## <a name="next-steps"></a>다음 단계

- 통합 문서에 대해 자세히 알아보려면 [통합 문서 개요](../visualize/workbooks-overview.md)를 방문하세요.
- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](./api-custom-events-metrics.md#trackevent) 또는 [페이지 보기](./api-custom-events-metrics.md#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [깔때기](usage-funnels.md)
    - [보존](usage-retention.md)
    - [사용자 흐름](usage-flows.md)
    - [통합 문서](../visualize/workbooks-overview.md)
    - [사용자 컨텍스트 추가](./usage-overview.md)