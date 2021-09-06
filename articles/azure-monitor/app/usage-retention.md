---
title: Application Insights를 사용한 웹앱 사용자 재방문 주기 분석
description: 앱으로 돌아온 사용자는 몇 명이나 되나요?
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: de83c898d7a471892f61fa299cdf2091a1f228e7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536302"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Application Insights를 사용한 웹 애플리케이션의 사용자 재방문 주기 분석

[Application Insights](./app-insights-overview.md)의 재방문 주기 기능은 앱으로 돌아온 사용자 수와 특정 작업을 수행하거나 목표를 달성하는 빈도를 분석하는 데 도움을 줍니다. 예를 들어 게임 사이트를 실행하는 경우 게임에서 진 후에 사이트로 돌아오는 사용자 수와 이긴 후에 돌아온 사용자 수를 비교할 수 있습니다. 이러한 지식이 있으면 사용자 환경 및 비즈니스 전략을 둘 다 향상시킬 수 있습니다.

## <a name="get-started"></a>시작하기

Application Insights 포털의 재방문 주기 도구에서 아직 데이터가 표시되지 않으면 [사용 도구 시작 방법을 알아봅니다](usage-overview.md).

## <a name="the-retention-workbook"></a>보존 통합 문서

보존 통합 문서를 사용하려면 Application Insights 리소스에서 **사용량** > **보존** 으로 이동하고 **보존 분석 통합 문서** 를 선택합니다. 또는 **통합 문서** 탭에서 **공개 템플릿** 을 선택한 다음, *사용* 에서 **사용자 보존 분석** 을 선택합니다.

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="공개 템플릿의 통합 문서 갤러리 스크린샷." lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>통합 문서 사용

:::image type="content" source="./media/usage-retention/retention.png" alt-text="꺾은선형 차트를 보여주는 보존 통합 문서의 스크린샷." lightbox="./media/usage-retention/retention.png":::

- 기본적으로 재방문 주기는 기간에 따라 작업을 한 후 다시 방문하고 다른 작업을 한 모든 사용자를 보여 줍니다. 다른 이벤트의 조합을 선택하여 특정 사용자 작업에 대한 범위를 좁힐 수 있습니다.
- **필터 추가** 를 선택하여 속성에 필터를 하나 이상 추가합니다. 예를 들어 특정 국가 또는 하위 지역에 있는 사용자에 집중할 수 있습니다. 
- 전체 재방문 주기 차트는 선택한 기간에 걸친 사용자 재방문 주기의 요약을 보여 줍니다. 
- 그리드는 보존된 사용자 수를 표시합니다. 각 행은 표시된 기간에 이벤트를 실행한 사용자의 코호트를 표시합니다. 행의 각 셀은 이후 기간에 1번 이상 돌아온 해당 코호트 수를 표시합니다. 일부 사용자는 둘 이상의 기간에 돌아올 수 있습니다. 
- insights 카드는 상위 5개 시작 이벤트 및 상위 5개 반환되는 이벤트를 표시하여 사용자가 자신의 재방문 주기 보고서를 더 잘 이해할 수 있도록 합니다. 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="#주 차트 이후의 사용자 반환을 보여주는 보존 통합 문서의 스크린샷." lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>비즈니스 이벤트를 사용하여 재방문 주기 추적

유용한 재방문 주기 분석 기능을 최대한 활용하려면 유의한 비즈니스 활동을 나타내는 이벤트를 측정합니다. 

예를 들어 많은 사용자는 앱에서 페이지를 열기만 하고 표시되는 게임을 재생하지 않을 수 있습니다. 따라서 페이지 보기만 추적하면 이전에 게임을 즐긴 후에 돌아와서 게임을 재생한 사용자 수를 정확히 추정할 수 없습니다. 돌아온 플레이어를 명확히 분선하기 위해 앱은 사용자가 실제로 게임을 할 때 사용자 지정 이벤트를 전송해야 합니다.  

주요 비즈니스 작업을 나타내는 사용자 지정 이벤트를 코딩하고 재방문 주기 분석에 사용하는 것이 좋습니다. 게임 결과를 캡처하려면 Application Insights에 사용자 지정 이벤트를 전송하는 코드 줄을 작성해야 합니다. 웹 페이지 코드 또는 Node.JS에서 작성할 경우 다음과 같습니다.

```JavaScript
    appinsights.trackEvent("won game");
```

또는 ASP.NET 서버 코드:

```csharp
   telemetry.TrackEvent("won game");
```

[사용자 지정 이벤트 작성에 대해 자세히 알아봅니다](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>다음 단계
- - 통합 문서에 대해 자세히 알아보려면 [통합 문서 개요](../visualize/workbooks-overview.md)를 방문하세요.
- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](./api-custom-events-metrics.md#trackevent) 또는 [페이지 보기](./api-custom-events-metrics.md#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [사용자, 세션, 이벤트](usage-segmentation.md)
    - [깔때기](usage-funnels.md)
    - [사용자 흐름](usage-flows.md)
    - [통합 문서](../visualize/workbooks-overview.md)
    - [사용자 컨텍스트 추가](./usage-overview.md)