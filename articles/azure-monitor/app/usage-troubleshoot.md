---
title: 사용자 분석 도구 문제 해결 - Application Insights
description: 문제 해결 가이드 - Application Insights를 사용하여 사이트 및 앱 사용량 현황 분석
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 8bd40f64a8c0e7bf0cbbdc427b44dd83965c52db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536387"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Application Insights에서 사용자 동작 분석 도구 문제 해결
[Application Insights의 사용자 동작 분석 도구](usage-overview.md)에서 [사용자, 세션, 이벤트](usage-segmentation.md), [유입 경로](usage-funnels.md), [사용자 흐름](usage-flows.md), [재방문 주기](usage-retention.md) 또는 코호트에 대해 질문이 있으신가요? 다음은 몇 가지 대답입니다.

## <a name="counting-users"></a>사용자 수 계산
**사용자 동작 분석 도구에 따르면 내 앱에 하나의 사용자/세션이 있다고 표시되지만 내가 알기로 내 앱에는 여러 사용자/세션이 있습니다. 이러한 잘못된 수를 수정하려면 어떻게 해결할 수 있나요?**

Application Insights의 모든 원격 분석 이벤트에는 표준 속성 중 2가지인 [익명 사용자 ID](./data-model-context.md) 및 [세션 ID](./data-model-context.md)가 있습니다. 기본적으로 모든 사용량 현황 분석 도구는 이러한 ID를 기준으로 사용자 및 세션 수를 계산합니다. 이러한 표준 속성이 앱의 각 사용자 및 세션의 고유 ID로 채워지지 않을 경우 사용량 현황 분석 도구에 잘못된 수의 사용자 및 세션이 표시됩니다.

웹앱을 모니터링하는 경우 가장 쉬운 방법은 앱에 [Application Insights JavaScript SDK](./javascript.md)를 추가하고, 모니터링하려는 각 페이지에 스크립트 조각이 로드되는지 확인하는 것입니다. JavaScript SDK는 익명 사용자 및 세션 ID를 자동으로 생성한 다음, 앱에서 전송되는 원격 분석 이벤트를 이러한 ID로 채웁니다.

웹 서비스를 모니터링하는 경우(사용자 인터페이스 없음) 고유한 사용자 및 세션에 대한 서비스 개념에 따라, [익명 사용자 ID 및 세션 ID 속성을 채우는 원격 분석 이니셜라이저를 만듭니다](./usage-overview.md).

앱이 [인증된 사용자 ID](./api-custom-events-metrics.md#authenticated-users)를 전송하는 경우 사용자 도구에서 인증된 사용자 ID를 기준으로 수를 계산할 수 있습니다. "표시" 드롭다운 목록에서 "인증된 사용자"를 선택합니다.

사용자 동작 분석 도구는 현재, 익명 사용자 ID, 인증된 사용자 ID 또는 세션 ID 이외의 속성을 기준으로 사용자 또는 세션 수를 계산하는 것을 지원하지 않습니다.

## <a name="naming-events"></a>이벤트 이름 지정
**내 앱에는 수천 가지 페이지 보기 및 사용자 지정 이벤트 이름이 있습니다. 이러한 도구는 구분하기 어렵고 사용자 동작 분석 도구가 응답하지 않는 경우가 많습니다. 이러한 이름 지정 문제를 어떻게 해결할 수 있나요?**

페이지 보기 및 사용자 지정 이벤트 이름은 사용자 동작 분석 도구 전체에서 사용됩니다. 이벤트 이름을 잘 지정하는 것은 이러한 도구에서 값을 가져오는 데 매우 중요합니다. 그 목표는 너무 적은 과도하게 일반적인 이름("단추를 클릭함")도 아니고, 너무 많은 과도하게 구체적인 이름("http:\//www.contoso.com/index에서 편집 단추를 클릭함")도 아닌 적절한 이름을 사용하는 것입니다.

앱이 전송하는 페이지 보기 및 사용자 지정 이벤트 이름을 변경하려면 앱의 소스 코드를 변경하고 다시 배포해야 합니다. **Application Insights의 모든 원격 분석 데이터는 90일 동안 저장되며 삭제할 수 없으므로** 이벤트 이름 변경 내용이 완전히 반영되려면 90일이 걸립니다. 이름을 변경한 후 90일 동안, 이전 및 새 이벤트 이름이 모두 원격 분석에 표시되므로, 그에 따라 팀 내에서 쿼리를 조정하고 소통합니다.

앱이 너무 많은 페이지 보기 이름을 전송하는 경우, 이 페이지 보기 이름이 코드에 수동으로 지정되는지 또는 Application Insights JavaScript SDK에 의해 자동으로 전송되는지를 확인합니다.

* [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)를 사용하여 페이지 보기 이름을 코드에 수동으로 지정한 경우 이름을 더 구체적인 이름으로 변경합니다. 페이지 보기의 이름에 URL을 포함하는 것과 같은 일반적인 실수를 피하도록 합니다. 대신, `trackPageView` API의 URL 매개 변수를 사용합니다. 페이지 보기 이름의 기타 세부 정보를 사용자 지정 속성으로 이동합니다.

* Application Insights JavaScript SDK가 페이지 보기 이름을 자동으로 전송하는 경우, 페이지의 제목을 변경하거나 페이지 보기 이름을 수동으로 전송하도록 전환할 수 있습니다. SDK는 기본적으로 각 페이지의 [제목](https://developer.mozilla.org/docs/Web/HTML/Element/title)을 각 페이지 보기 이름으로 보냅니다. 제목을 좀 더 일반적인 것으로 변경할 수 있지만, 이러한 변경에 따른 SEO 및 기타 영향에 주의해야 합니다. `trackPageView` API를 사용하여 페이지 보기 이름을 수동으로 지정하면 자동으로 수집된 이름이 재정의되므로 페이지 제목을 변경하지 않으면서, 원격 분석에서 보다 일반적인 이름을 전송할 수 있습니다.   

앱이 너무 많은 사용자 지정 이벤트 이름이 전송하는 경우, 코드의 이름을 덜 구체적인 이름으로 변경합니다. 마찬가지로, 사용자 지정 이벤트 이름에 URL과 기타 페이지별 또는 동적 정보를 직접 추가하지 않도록 합니다. 대신, `trackEvent` API를 사용하여 이러한 세부 정보를 사용자 지정 이벤트의 사용자 지정 속성으로 이동합니다. 예를 들어, `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` 대신, `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`와 같은 방식이 권장됩니다.

## <a name="next-steps"></a>다음 단계

* [사용자 동작 분석 도구 개요](usage-overview.md)

## <a name="get-help"></a>도움말 보기
* [스택 오버플로](https://stackoverflow.com/questions/tagged/ms-application-insights)