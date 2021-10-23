---
title: Application Insights에서의 고객 이해 | Microsoft Docs
description: Application Insights를 사용하여 고객이 애플리케이션을 사용하는 방법을 이해하는 자습서입니다.
ms.topic: tutorial
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: f06522ffe257bc7e20fe587b7c0a4479f6677240
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129627"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Azure Application Insights를 사용하여 고객이 애플리케이션을 사용하는 방법 이해

 Application Insights는 사용량 정보를 수집하여 사용자 애플리케이션과 상호 작용하는 방법을 이해할 수 있도록 돕습니다.  이 자습서에서는 이 정보를 분석하는 데 사용할 수 있는 다양한 리소스를 안내합니다.  이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 애플리케이션에 액세스하는 사용자에 대한 세부 정보 분석
> * 세션 정보를 사용하여 고객이 애플리케이션을 사용하는 방법 분석
> * 원하는 사용자 작업을 실제 작업과 비교할 수 있도록 하는 깔때기 정의 
> * 통합 문서를 만들어 시각화 및 쿼리를 단일 문서로 통합
> * 유사한 사용자를 그룹화하여 함께 분석
> * 애플리케이션으로 돌아오는 사용자 알아보기
> * 사용자가 애플리케이션을 통해 탐색하는 방법 검사


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드로 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- [Visual Studio 스냅샷 디버거](https://aka.ms/snapshotdebugger)를 다운로드 및 설치합니다.
- Azure에 .NET 애플리케이션을 배포하고 [Application Insights SDK를 사용하도록 설정](../app/asp-net.md)합니다. 
- 사용자 지정 이벤트/페이지 보기 추가에 대한 [애플리케이션에서 원격 분석 전송](../app/usage-overview.md#send-telemetry-from-your-app)
- [사용자 컨텍스트](./usage-overview.md)를 전송하여 시간에 따라 사용자 작업을 추적하고 사용량 기능을 최대한 활용합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.

## <a name="get-information-about-your-users"></a>사용자에 대한 정보 가져오기
**사용자** 패널을 통해 여러 가지 방법으로 사용자에 대한 중요한 세부 정보를 이해할 수 있습니다. 이 패널을 사용하여 사용자가 연결하는 위치, 해당 클라이언트의 세부 정보 및 액세스하는 애플리케이션의 영역과 같은 정보를 이해합니다. 

1. Application Insights의 *구성* 에서 메뉴의 **사용자** 를 선택합니다.
2. 기본 보기는 지난 24시간 동안 애플리케이션에 연결한 고유 사용자 수를 표시합니다.  시간 창을 변경할 수 있으며 다양한 조건을 설정하여 이 정보를 필터링할 수 있습니다.

3. **기간** 드롭다운을 클릭하고 시간 창을 7일로 변경합니다.  그러면 패널에서 다른 차트에 포함된 데이터를 증가시킵니다.

4. **분할 기준** 드롭다운을 클릭하여 그래프에 사용자 속성으로 분석을 추가합니다.  **국가 또는 지역** 을 선택합니다.  그래프는 동일한 데이터를 포함하지만 각 국가/지역에 대한 사용자 수의 분석을 볼 수 있습니다.

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="사용자 탭의 쿼리 작성기 스크린샷." lightbox="./media/tutorial-users/user-1.png":::

5. 차트의 다른 막대 위로 커서를 배치하고 각 국가/지역에 대한 수가 해당 막대로 표시된 시간 창만 반영하는지 확인합니다.
6. 자세한 내용을 보려면 **인사이트 더 보기** 를 선택합니다. 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="인사이트 더 보기의 사용자 탭 스크린샷." lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>사용자 세션 분석
**세션** 패널은 **사용자** 패널과 유사합니다.  여기서 **사용자** 는 애플리케이션에 액세스하는 사용자에 대한 세부 정보를 이해하도록 돕고, **세션** 은 해당 사용자가 애플리케이션을 사용한 방법을 이해하도록 돕습니다.  

1. 사용자 *사용량* 에서 **세션** 을 선택합니다.
2. 그래프를 확인하고 필터링하는 동일한 옵션이 있는지 확인하고 **사용자** 패널에서와 같이 데이터를 분석합니다.

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="막대형 차트가 표시된 세션 탭의 스크린샷." lightbox="./media/tutorial-users/sessions.png":::

4. 세션 타임라인을 보려면 **인사이트 더 보기** 를 선택한 다음, 활성 세션의 타임라인 중 하나 위에서 **세션 타임라인 보기** 를 선택합니다. 세션 타임라인은 세션의 모든 작업을 표시합니다. 이를 통해 많은 수의 예외가 있는 세션과 같은 정보를 확인할 수 있습니다.

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="타임라인이 선택된 세션 탭의 스크린샷." lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>비슷한 사용자를 함께 그룹화
**코호트** 는 비슷한 특성에 대해 그룹화된 사용자의 집합입니다.  코호트를 사용하여 특정 사용자 그룹을 분석할 수 있도록 다른 패널에서 데이터를 필터링할 수 있습니다.  예를 들어, 구매를 완료한 사용자만 분석하려 할 수 있습니다.

1.  사용량 탭(사용자, 세션, 이벤트 등) 중 하나 위에서 **코호트 만들기** 를 선택합니다.

1.  갤러리에서 템플릿을 선택합니다.

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="코호트의 템플릿 갤러리 스크린샷." lightbox="./media/tutorial-users/cohort.png":::
1.  코호트를 편집한 다음, **저장** 을 선택합니다.
1.  코호트를 보려면 **표시** 드롭다운 메뉴에서 선택합니다. 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="코호트를 보여주는 보기 드롭다운 스크린샷." lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>원하는 작업과 실제 비교
이전 패널이 애플리케이션의 사용자가 수행한 작업에 집중한 반면 **깔때기** 는 사용자가 수행하길 원하는 작업에 집중합니다.  깔때기는 애플리케이션에서 단계의 집합 및 단계 간을 이동하는 사용자의 백분율을 나타냅니다.  예를 들어, 애플리케이션에 연결하고 제품을 검색하는 사용자의 백분율을 측정하는 깔때기를 만들 수 있습니다.  그런 다음 해당 제품을 쇼핑 카트에 추가한 사용자의 백분율 및 구매를 완료하는 사용자의 백분율을 볼 수 있습니다.

1. 메뉴에서 **유입 경로** 를 선택한 다음, **편집** 을 클릭합니다. 

3. 각 단계에 대한 작업을 선택하여 두 개 이상의 단계로 깔때기를 만듭니다.  작업 목록은 Application Insights에서 수집한 사용량 데이터에서 만들어집니다.

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="유입 경로 탭의 스크린샷 및 수정 탭에서 단계 선택" lightbox="./media/tutorial-users/funnel.png":::

4. 결과를 보려면 **보기** 탭을 선택합니다. 오른쪽 창은 첫 번째 작업 전과 마지막 작업 후에 가장 일반적인 이벤트를 보여 주어 특정 시퀀스에 대한 사용자 경향을 이해할 수 있도록 돕습니다.

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="보기의 유입 경로 탭 스크린샷." lightbox="./media/tutorial-users/funnel-2.png":::

4. 유입 경로를 저장하려면 **저장** 을 선택합니다. 

## <a name="learn-which-customers-return"></a>돌아오는 고객 알아보기

**재방문 주기** 를 통해 애플리케이션으로 다시 돌아오는 사용자를 이해할 수 있습니다.  

1. 메뉴에서 **보존** 을 선택한 다음, *보존 분석 통합 문서를 선택합니다.
2. 기본적으로 분석된 정보는 작업을 수행한 다음 작업을 수행하기 위해 돌아온 사용자를 포함합니다.  이 필터를 다음을 포함하도록, 예를 들어, 구매를 완료한 후 돌아온 사용자만 포함하도록 변경할 수 있습니다.

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="보존 필터에 설정된 조건과 일치하는 사용자에 대한 그래프를 보여주는 스크린샷." lightbox="./media/tutorial-users/retention.png":::

3. 조건과 일치하는 돌아온 사용자는 서로 다른 기간에 대한 그래픽 및 테이블 형식으로 표시됩니다. 일반적인 패턴은 시간에 따라 사용자가 돌아오는 점진적 하락에 대한 것입니다.  한 기간에서 다음 기간으로의 갑작스런 하락은 문제를 야기할 수 있습니다. 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="#주 차트 이후의 사용자 반환을 보여주는 보존 통합 문서의 스크린샷." lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>사용자 탐색 분석
**사용자 흐름** 은 페이지와 애플리케이션의 기능 간에 사용자가 탐색하는 방법을 시각화합니다.  이 기능을 사용하면 사용자가 일반적으로 특정 페이지에서 이동하는 위치, 일반적으로 애플리케이션을 종료하는 방법 및 정기적으로 반복되는 작업이 있는지와 같은 질문에 답할 수 있습니다.

1.  메뉴에서 **사용자 흐름** 을 선택합니다.
2.  **새로 만들기** 를 클릭하여 새 사용자 흐름을 만든 다음, **편집** 을 선택하여 해당 세부 정보를 편집합니다.
3.  **시간 범위** 를 7일로 증가시킨 다음 초기 이벤트를 선택합니다.  흐름은 해당 이벤트로 시작하는 사용자 세션을 추적합니다.

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="새 사용자 흐름을 만드는 방법을 보여주는 스크린샷." lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  사용자 흐름이 표시되고 다른 사용자 경로 및 해당 세션 수를 볼 수 있습니다.  파란색 선은 현재 작업 후에 사용자가 수행한 작업을 나타냅니다.  빨간색 선은 사용자 세션의 끝을 나타냅니다.

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="사용자 흐름에 대한 사용자 경로 및 세션 수의 표시를 보여주는 스크린샷." lightbox="./media/tutorial-users/flows.png":::

5.  흐름에서 이벤트를 제거하려면 작업 모서리에서 **x** 를 선택한 다음, **그래프 만들기** 를 선택합니다.  그래프가 제거된 해당 이벤트의 모든 인스턴스로 다시 그려집니다. **편집** 을 선택하여 이벤트가 이제 **제외된 이벤트** 에 추가되었는지 확인합니다.

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="사용자 흐름에 대해 제외된 이벤트 목록을 보여주는 스크린샷." lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>사용량 데이터 통합
**통합 문서** 는 데이터 시각화, Analytics 쿼리 및 텍스트를 대화형 문서로 결합합니다.  통합 문서를 사용하여 일반적인 사용량 정보를 함께 그룹화하고, 특정 오류로부터 정보를 통합하거나 애플리케이션의 사용량에 대해 팀에게 다시 보고할 수 있습니다.

1.  메뉴에서 **통합 문서** 를 선택합니다.
2.  **새로 만들기** 를 선택하여 새 통합 문서를 만듭니다.
3.  막대형 차트로 표시된 마지막 날짜의 모든 사용량 데이터를 포함하는 쿼리가 이미 제공됩니다.  이 쿼리를 사용하여 수동으로 편집하거나 **샘플 쿼리** 를 선택하여 다른 유용한 쿼리에서 선택할 수 있습니다.

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="사용할 수 있는 샘플 쿼리 목록과 샘플 단추를 보여주는 스크린샷." lightbox="./media/tutorial-users/sample-queries.png":::

4.  **편집 완료** 를 선택합니다.
5.  위쪽 창에서 **편집** 을 선택하여 통합 문서 맨 위에 있는 텍스트를 편집합니다.  Markdown을 사용하여 형식이 지정됩니다.

6.  **사용자 추가** 를 선택하여 사용자 정보를 포함하는 그래프를 추가합니다.  원하는 경우 그래프의 세부 정보를 편집한 다음, **편집 완료** 를 선택하여 저장합니다.

통합 문서에 대한 자세한 내용은 [통합 문서 개요](../visualize/workbooks-overview.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계
이제 사용자를 분석하는 방법을 배웠으므로 이 정보를 애플리케이션에 대한 다른 유용한 데이터와 결합하는 사용자 지정 대시보드를 만드는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 대시보드 만들기](./tutorial-app-dashboards.md)