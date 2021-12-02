---
title: 하트 분석 통합 문서
description: 제품 팀은 하트 통합 문서를 사용 하 여 5 개의 사용자 중심 차원에서 성공을 측정 하 여 더 나은 소프트웨어를 제공 합니다.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 2ad13ebdde8dd504289ad810c8bdf3c11184420f
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487824"
---
# <a name="analyzing-product-usage-with-heart"></a>하트로 제품 사용 분석
이 문서에서는 Azure Monitor에서 하트 통합 문서를 사용 하도록 설정 하 고 사용 하는 방법을 설명 합니다. 하트 통합 문서는 처음에 Google에서 도입 된 하트 측정 프레임 워크를 기반으로 합니다. 여러 Microsoft 내부 팀은 하트를 사용 하 여 더 나은 소프트웨어를 제공 합니다.

 
## <a name="overview"></a>개요
하트는 행복, 참여, 도입, 보존 및 작업 성공 등을 의미 하는 머리글자어입니다. 제품 팀은 다음과 같은 5 가지 사용자 환경에 집중 하 여 더 나은 소프트웨어를 제공할 수 있습니다.

 

- **만족도**: 사용자의 태도 측정  
- **참여**: 활성 사용자 참여 수준 
- **채택**: 대상 그룹 침투
- **보존**: 사용자가 반환 하는 속도  
- **작업 성공**: 생산성 강화 

이러한 차원은 독립적으로 측정 되지만 아래와 같이 서로 상호 작용 합니다.

:::image type="content" source="media/usage-overview/heart-funnel.png" alt-text="하트 차원 간의 깔때기 관계를 표시 하는 이미지입니다. 깔때기 경로는 다음과 같습니다. 약정에 대 한 약정에 대 한 채택을 도입 합니다. 작업 성공은이 깔때기형의 드라이버입니다.":::



- 채택, 참여 및 보존은 사용자 활동 깔때기를 형성 합니다. 도구를 채택 하는 일부 사용자만이 도구를 사용 합니다.
- 작업 성공은 사용자가 깔때기를 진행 하 고 채택에서 보존으로 이동 하는 드라이버입니다.
- 행복은 다른 차원의 결과 이며 독립 실행형 측정값이 아닙니다. 깔때기를 종료 하 고 더 높은 수준의 활동을 표시 하는 사용자는 가장 만족 해야 합니다.   


## <a name="get-started"></a>시작
### <a name="prerequisites"></a>사전 요구 사항
 - Azure 구독: [무료로 azure 구독 만들기](https://azure.microsoft.com/free/)
 - Application Insights 리소스: [Application Insights 리소스 만들기](create-workspace-resource.md#create-workspace-based-resource)
 - 아래 특성을 계측 하 여 하트 메트릭을 계산 합니다.

  | 원본          | attribute            | 설명                                |
  |-----------------|----------------------|--------------------------------------------|
  | customEvents    | user_AuthenticatedId | 고유 인증 사용자 식별자       |
  | customEvents    | session_Id           | 고유 세션 식별자                  |
  | customEvents    | appName              | 고유 Application Insights 앱 식별자 |
  | customEvents    | itemType             | CustomEvents 레코드의 범주            |
  | customEvents    | timestamp            | 이벤트의 Datetime                          |
  | customEvents    | operation_Id         | 원격 분석 이벤트 상관 관계                 |
  | customEvents    | user_Id               | 고유한 사용자 id                      |
  | CustomEvents   | parentId             | 기능의 이름                            |
  | CustomEvents   | pageName             | 페이지 이름                               |
  | CustomEvents   | actionType           | 클릭 분석 레코드의 범주         |
  | pageViews       | user_AuthenticatedId | 고유 인증 사용자 식별자       |
  | pageViews       | session_Id           | 고유 세션 식별자                  |
  | pageViews       | appName              | 고유 Application Insights 앱 식별자 |
  | pageViews       | timestamp            | 이벤트의 Datetime                          |
  | pageViews       | operation_Id         | 원격 분석 이벤트 상관 관계                 |
  | pageViews       | user_Id                | 고유한 사용자 id                     |

* Npm을 통해 [클릭 분석 자동 수집 플러그 인](javascript-click-analytics-plugin.md) 을 사용 하 여 이러한 특성을 내보냅니다.

>[!TIP]
> 클릭 분석 플러그 인을 효과적으로 사용 하는 방법을 알아보려면 [이 섹션](javascript-click-analytics-plugin.md#how-to-effectively-use-the-plugin)을 참조 하세요.
 
### <a name="open-the-workbook"></a>통합 문서 열기
통합 문서는 갤러리의 ' 공용 템플릿 ' 아래에서 찾을 수 있습니다. 통합 문서는 다음 그림에 표시 된 것 처럼 **"클릭 분석 플러그 인을 사용 하는 제품 분석"** 섹션에 표시 됩니다.

:::image type="content" source="media/usage-overview/workbook-gallery.png" alt-text="Azure 앱 Insights의 하트 통합 문서 위치를 강조 표시 하는 스크린샷":::

사용자는 다음 이미지와 같이 7 개의 통합 문서가 있음을 알 수 있습니다.

:::image type="content" source="media/usage-overview/heart-workbook-templates.png" alt-text="Azure 앱 Insights ' 통합 문서 섹션의 공용 템플릿에서 7 개의 하트 통합 문서 이름을 강조 표시 하는 스크린샷":::


통합 문서는 사용자가 기본 통합 문서인 ' 하트 분석-모든 섹션 '과 상호 작용 해야 하는 방식으로 디자인 되었습니다. 이 통합 문서에는 6 개의 통합 문서를 탭으로 포함 합니다. 필요한 경우 사용자는 갤러리를 통해 연결 탭과 관련 된 개별 통합 문서에도 액세스할 수 있습니다.


### <a name="confirm-data-is-flowing"></a>데이터 흐름 확인

아래와 같이 "개발 요구 사항" 탭을 참조 하 여 데이터가 예상 대로 흐르는 지 유효성을 검사 하 여 메트릭을 정확 하 게 확인 합니다. 

:::image type="content" source="media/usage-overview/development-requirements-1.png" alt-text="' 하트 분석-모든 섹션 ' 통합 문서의 ' 개발 요구 사항 ' 탭을 강조 표시 하는 스크린샷":::

데이터가 예상 대로 이동 하지 않는 경우이 탭에는 아래 예제와 같이 문제가 있는 특정 특성이 강조 표시 됩니다.

:::image type="content" source="media/usage-overview/development-requirements-2.png" alt-text="하트 통합 문서의 ' 개발 요구 사항 ' 탭에서 데이터 불일치를 강조 표시 하는 스크린샷":::


## <a name="workbook-structure"></a>통합 문서 구조
이 통합 문서에서는 8 개 탭에서 분할 된 하트 차원에 대 한 메트릭 추세를 보여 줍니다. 각 탭에는 차원, 각 차원에 포함 된 메트릭 및 사용 방법에 대 한 설명이 포함 되어 있습니다.

아래에서 탭에 대 한 간략 한 설명을 볼 수 있습니다. 

- **요약 탭** -사용 깔때기형 메트릭에는 방문, 상호 작용 및 반복 사용에 대 한 개략적인 보기가 제공 됩니다. 
- **채택** -이 탭은 대상 사용자, 획득 속도 및 총 사용자 기반 간의 침투를 이해 하는 데 도움이 됩니다. 
- **Engagement** -빈도, 깊이 및 사용량의 범위입니다. 
- **보존** -반복 사용 
- **작업 성공** -사용자 흐름과 해당 시간 배포에 대 한 이해를 가능 하 게 합니다. 
- **만족도** -설문 조사 도구를 사용 하 여 5 포인트 배율로 고객 만족도 점수 (csat)를 측정 하는 것이 좋습니다. 이 탭에서는 사용량 및 성능 메트릭을 사용 하 여 만족도를 제공 합니다. 
- **기능 메트릭** -기능 세분성에서 하트 메트릭을 이해할 수 있습니다. 

> [!WARNING]
> 하트 통합 문서는 현재 로그를 기반으로 작성 되었으며 효과적으로 [로그 기반 메트릭입니다](pre-aggregated-metrics-log-metrics.md). 이러한 메트릭의 정확도는 샘플링 및 필터링에 의해 부정적인 영향을 받습니다.
## <a name="how-heart-dimensions-are-defined-and-measured"></a>하트 차원을 정의 하 고 측정 하는 방법

### <a name="happiness"></a>행복

행복은 사용자가 제공 하는 제품에 대 한 사용자의 느낌을 측정 하는 사용자 보고 차원입니다. 

만족도를 측정 하는 일반적인 방법은 사용자에 게 *이 제품에 만족* 하는 것과 같은 고객 만족도 (csat) 질문을 하는 것입니다. 3 또는 5 포인트 규모의 사용자 응답 (예: *아니요,* 5, *예* 등)은 1-5에서 제품 수준 점수를 생성 하도록 집계 됩니다. 사용자가 시작한 피드백은 부정적인 편향이 되기 때문에 하트는 미리 정의 된 간격으로 사용자에 게 표시 되는 설문 조사에서 만족도를 추적 합니다.

일반적인 만족도 메트릭에는 *평균 별 등급* 및 *고객 만족도 점수* 와 같은 값이 포함 됩니다. [사용자 지정 원본](../agents/data-sources.md#custom-sources)에 설명 된 사용자 지정 수집 방법 중 하나를 사용 하 여 Azure Monitor에 이러한 값을 보냅니다.




### <a name="engagement"></a>참여
Engagement는 클릭과 같은 의도적인 사용자 작업, 특히 의도적인 사용자 작업을 측정 한 것입니다. 활성 사용량을 세 개의 하위 치수로 나눌 수 있습니다.
- **활동 빈도** – 사용자가 제품과 상호 작용 하는 빈도를 측정 합니다. 예를 들어 사용자는 일반적으로 매일, 매주 또는 매월 상호 작용 합니다.
- **활동 범위** – 지정 된 기간 동안 사용자가 상호 작용 하는 기능 수를 측정 합니다. 예를 들어 사용자가 6 월 2021에 총 5 개 기능과 상호 작용 했습니다.
- **활동 수준** – 제품을 시작할 때마다 사용자가 상호 작용 하는 기능 수를 측정 합니다. 예를 들어 사용자가 모든 시작 시 두 가지 기능으로 상호 작용 했습니다.

Engagement 측정은 사용 중인 제품 유형에 따라 달라질 수 있습니다. 예를 들어 Microsoft Teams와 같은 제품은 높은 일일 사용량이 있으므로 추적 하는 데 중요 한 메트릭이 될 것으로 예상 됩니다. 그러나 paycheck 포털과 같은 제품의 경우 단위는 월별 또는 주별 수준에서 보다 합리적입니다.

>[!IMPORTANT]
>단추를 클릭 하거나 입력을 입력 하는 등의 의도적인 작업을 수행 하는 사용자는 활성 사용자로 계산 됩니다. 이러한 이유로 Engagement 메트릭에는 응용 프로그램에서 구현 되는 [Application Insights에 대 한 클릭 분석 플러그 인](javascript-click-analytics-plugin.md) 이 필요 합니다.



### <a name="adoption"></a>채택

채택을 통해 관련 사용자에 대 한 이해를 이해 하 고, 사용자의 기반이 되는 사용자와 사용자의 의견을 얻을 수 있습니다. 도입 메트릭은 아래 시나리오를 측정 하는 데 유용 합니다.

- 새로 릴리스된 제품  
- 새로 업데이트 된 제품  
- 마케팅 캠페인  



### <a name="retention"></a>보존
보존 된 사용자는 지정 된 보고 기간 및 이전 보고 기간에 활성화 된 사용자입니다. 보존은 일반적으로 다음과 같은 메트릭에 따라 측정 됩니다.

| 메트릭         | 정의                                                                          | 질문과 대답                                              |
|----------------|-------------------------------------------------------------------------------------|----------------------------------------------------------------|
| 보유 한 사용자 | 이전 기간에도 활성화 된 활성 사용자 수                      | 제품을 사용 하 고 있는 사용자는 몇 개입니까?        |
| 보존      | 이 기간 동안 활성 상태인 이전 기간의 활성 사용자 비율 | 제품을 사용 하 고 있는 사용자는 어느 정도 인가요? |

>[!IMPORTANT]
>활성 사용자에 게 actionType이 있는 원격 분석 이벤트가 하나 이상 있어야 하므로 보존 메트릭에는 응용 프로그램에서 구현 되는 [Application Insights에 대 한 클릭 분석 플러그 인](javascript-click-analytics-plugin.md) 이 필요 합니다.


### <a name="task-success"></a>작업 성공
작업 성공은 사용자가 제품의 기능을 효율적이 고 효과적으로 사용할 수 있는지 여부를 추적 합니다. 많은 제품에는 사용자가 작업을 완료 하는 과정을 안내 하도록 디자인 된 구조가 포함 되어 있습니다. 예를 들면 다음과 같습니다.
- 카트에 항목을 추가한 다음 구매를 완료 합니다.
- 키워드를 검색 한 다음 결과를 클릭 합니다.
- 새 계정을 시작한 후 계정 등록을 완료 합니다.

성공 작업은 다음과 같은 세 가지 요구 사항을 충족 합니다.
- 예상 된 작업 흐름-기능의 의도 된 작업 흐름이 사용자에 의해 완료 되었으며 예상 되는 태스크 흐름과 일치 합니다.
- 고성능-기능의 의도 된 기능을 적절 한 시간 내에 수행 했습니다.
- 높은 안정성 – 기능의 의도 된 기능은 오류 없이 수행 되었습니다.

위의 요구 사항 중 하나라도 충족 되지 않으면 작업이 실패 한 것으로 간주 됩니다.

>[!IMPORTANT]
>작업 성공 메트릭에는 응용 프로그램에서 구현 되는 [Application Insights에 대 한 클릭 분석 플러그 인](javascript-click-analytics-plugin.md) 이 필요 합니다.

아래 매개 변수를 사용 하 여 사용자 지정 태스크를 설정 합니다.

| 매개 변수         | 설명                                                                                                                                                                                                                         |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 첫 번째 단계           | 작업을 시작 하는 기능입니다. 위의 카트/구매 예제를 사용 하 여 "카트에 항목을 추가" 하는 것이 첫 번째 단계입니다.                                                                                                          |
| 예상 작업 기간 | 완료 된 작업의 성공 여부를 고려 하는 시간 창입니다. 이 제약 조건 외부에서 완료 된 모든 작업은 실패로 간주 됩니다. 모든 작업에 시간 제약 조건이 있는 것은 아닙니다. 이러한 작업의 경우 "시간 예상 없음"을 선택 합니다. |
| 마지막 단계        | 작업을 완료 하는 기능입니다. 위의 카트/구매 예제를 사용 하 여 "장바구니에서 품목 구매"가 마지막 단계입니다.                                                                                               |










## <a name="faqs"></a>FAQ

### <a name="how-do-i-view-the-data-at-different-grains-daily-monthly-weekly"></a>다른 조직에서 데이터를 볼 어떻게 할까요? 있나요? (매일, 매월, 매주)?
' 날짜 그레인 ' 필터를 클릭 하 여 아래와 같이 그레인을 변경할 수 있습니다.

:::image type="content" source="media/usage-overview/date-grain-monthly.png" alt-text="통합 문서에서 날짜 수준을 매일, 매월 또는 매주로 변경 하는 필터를 강조 표시 하는 스크린샷 모든 차원 탭에서 날짜 그레인 필터를 사용할 수 있습니다.":::


### <a name="how-do-i-access-insights-from-my-application-that-arent-available-on-the-heart-workbooks"></a>하트 통합 문서에서 사용할 수 없는 응용 프로그램의 정보에 액세스 어떻게 할까요??

시각적 개체가 모든 질문에 대답 하지 않으면 하트 통합 문서를 공급 하는 데이터를 자세히 살펴볼 수 있습니다. 이 작업을 수행 하려면 ' 모니터링 ' 섹션 아래의 ' 로그 '로 이동 하 여 customEvents 테이블을 쿼리 합니다. 일부 클릭 분석 특성은 customDimensions 필드에 포함 되어 있습니다. 예제 쿼리는 아래 이미지에 나와 있습니다.

:::image type="content" source="media/usage-overview/log-query-faq.png" alt-text="앱 Insights의 ' 모니터링 ' 아래에 있는 로그 섹션을 강조 표시 하는 스크린샷 또한 응용 프로그램 데이터를 검색 하기 위해 로그 섹션에 샘플 쿼리를 표시 합니다.":::

[Azure Monitor 로그 개요](../logs/data-platform-logs.md) 페이지로 이동 하 여 Azure Monitor 로그에 대해 자세히 알아보세요.


### <a name="can-i-edit-visuals-in-the-workbook"></a>통합 문서에서 시각적 개체를 편집할 수 있나요?

예, 통합 문서의 공용 템플릿을 클릭 하면 왼쪽 위 모서리로 이동 하 여 편집을 클릭 하 고 변경할 수 있습니다.

:::image type="content" source="media/usage-overview/workbook-edit-faq.png" alt-text="통합 문서 템플릿 위쪽의 ' 편집 ' 단추를 강조 표시 하는 스크린샷":::


변경한 후 ' 편집 완료 '를 클릭 한 다음 ' 저장 ' 아이콘을 클릭 합니다.

:::image type="content" source="media/usage-overview/workbook-save-faq.png" alt-text="편집 후 사용할 수 있게 되는 통합 문서 템플릿 위쪽의 ' 저장 ' 아이콘을 강조 표시 하는 스크린샷":::


저장 된 통합 문서를 보려면 ' 모니터링 ' 아래의 ' 통합 문서 ' 섹션으로 이동한 다음 ' 공용 템플릿 ' 탭 대신 ' 통합 문서 ' 탭을 클릭 합니다. 사용자 지정 된 통합 문서의 복사본이 표시 됩니다 (아래 참조). 이 특정 복사본에 대해 원하는 추가 변경 작업을 수행할 수 있습니다.

:::image type="content" source="media/usage-overview/workbook-view-faq.png" alt-text="편집 된 통합 문서 복사본을 찾을 수 있는 ' 공용 템플릿 ' 탭 옆의 ' 통합 문서 ' 단추를 강조 표시 하는 스크린샷":::

통합 문서 템플릿을 편집 하는 방법에 대 한 자세한 내용은 [통합 문서 템플릿 탐색](../visualize/workbooks-overview.md#exploring-a-workbook-template) 페이지를 참조 하세요.


 

## <a name="next-steps"></a>다음 단계
- Npm을 통해 [클릭 분석 자동 수집 플러그 인](javascript-click-analytics-plugin.md) 을 설정 합니다.
- 클릭 분석 자동 컬렉션 플러그 인에 대 한 [GitHub 리포지토리](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) 및 [NPM 패키지](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) 를 확인 합니다.
- [사용 환경의 이벤트 분석](usage-segmentation.md)을 사용하여 사용 가능한 차원별 상위 클릭 및 슬라이스를 분석합니다.
- [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query)의 CustomEvents 테이블에 있는 customDimensions 특성 내 콘텐츠 필드에서 데이터 클릭을 찾습니다. 자세한 지침은 [샘플 앱](https://go.microsoft.com/fwlink/?linkid=2152871) 을 참조 하세요.
- [Google의 핵심 프레임 워크](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/36299.pdf)에 대해 자세히 알아보세요.

 

 
