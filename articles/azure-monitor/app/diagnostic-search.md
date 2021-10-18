---
title: Azure Application Insights에서 Search 사용 | Microsoft Docs
description: 웹앱에서 전송된 원시 원격 분석을 검색하고 필터링합니다.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8a025210fc399c1d36fa416c3a4795331eca2293
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134206"
---
# <a name="using-search-in-application-insights"></a>Application Insights에서 Search 사용

트랜잭션 검색은 페이지 보기, 예외 또는 웹 요청과 같은 개별 원격 분석 항목을 찾고 탐색하는 데 사용하는 [애플리케이션](./app-insights-overview.md) Insights 기능입니다. 또한 코딩한 로그 추적 및 이벤트를 볼 수 있습니다.

(사용자 데이터에 비해 좀 더 복잡한 쿼리를 위해서는 [Analytics](../logs/log-analytics-tutorial.md)를 사용합니다.)

## <a name="where-do-you-see-search"></a>Search 기능은 어디에 있나요?

### <a name="in-the-azure-portal"></a>Azure Portal에서

위쪽 표시줄에 있는 애플리케이션의 애플리케이션 Insights 개요 탭 또는 왼쪽의 조사 아래에서 트랜잭션 검색을 열 수 있습니다.

![검색 탭](./media/diagnostic-search/view-custom-events.png)

이벤트 유형 드롭다운 메뉴로 이동하여 원격 분석 항목의 목록(서버 요청, 페이지 보기, 코딩한 사용자 지정 이벤트 등)을 볼 수 있습니다. 결과 목록의 위쪽은 시간이 지남에 따른 이벤트의 수를 보여 주는 요약 차트입니다.

드롭다운 메뉴를 클릭하거나 새로 고쳐서 새 이벤트를 가져옵니다.

### <a name="in-visual-studio"></a>Visual Studio

Visual Studio에는 Application Insights Search 창도 있습니다. 이 창은 디버그 중인 애플리케이션에 의해 생성된 원격 분석 이벤트를 표시하는 데 가장 유용합니다. 하지만 게시된 앱에서 수집된 이벤트를 Azure Portal에 표시할 수도 있습니다.

Visual Studio에서 Search 창을 엽니다.

![Visual Studio에서 열린 Application Insights 검색](./media/diagnostic-search/32.png)

Search 창은 웹 포털과 비슷한 기능을 제공합니다.

![Visual Studio Application Insights 검색 창](./media/diagnostic-search/34.png)

추적 작업 탭은 요청 또는 페이지 보기를 여는 경우에 사용할 수 있습니다. '연산'은 단일 요청 또는 페이지 보기와 연결된 이벤트의 시퀀스입니다. 예를 들어 종속성 호출, 예외, 추적 로그 및 사용자 지정 이벤트는 단일 연산의 일부일 수 있습니다. 추적 연산 탭에는 요청 또는 페이지 보기와 관련해서 이러한 이벤트의 타이밍 및 기간을 그래픽으로 보여 줍니다.

## <a name="inspect-individual-items"></a>개별 항목 검사

키 필드 및 관련 항목을 보려면 원격 분석 항목을 선택합니다.

![개별 종속성 요청 스크린샷](./media/diagnostic-search/telemetry-item.png)

이렇게 하면 엔드투엔드 트랜잭션 세부 정보 보기가 시작됩니다.

## <a name="filter-event-types"></a>이벤트 유형 필터링

이벤트 유형 드롭다운 메뉴를 열고 보려는 이벤트 유형을 선택합니다. (나중에 필터를 복원하려면 다시 설정을 클릭합니다.)

이벤트 유형은 다음과 같습니다.

* **추적** -  TrackTrace, log4Net, NLog 및 System.Diagnostic.Trace 호출을 포함한 [진단 로그](./asp-net-trace-logs.md)입니다.
* **요청** - 페이지, 스크립트, 이미지, 스타일 파일 및 데이터를 포함하는 서버 애플리케이션이 수신하는 HTTP 요청입니다. 이러한 이벤트는 요청을 만들고 개요 차트에 응답하는 데 사용됩니다.
* **페이지 보기** - [웹 클라이언트가 전송한 원격 분석](./javascript.md)이며, 페이지 보기 보고서를 만드는 데 사용됩니다.
* **사용자 지정 이벤트** - [사용량을 모니터링](./api-custom-events-metrics.md)하기 위해 TrackEvent()에 호출을 삽입한 경우 여기에서 검색할 수 있습니다.
* **예외 사항** - [서버에서 확인할 수 없는 예외 사항](./asp-net-exceptions.md)이며 TrackException()을 사용하여 로그합니다.
* **종속성** - [서버 애플리케이션에서](./asp-net-dependencies.md) REST API 또는 데이터베이스 등의 다른 서비스로의 호출 및 [클라이언트 코드](./javascript.md)에서의 AJAX 호출입니다.
* **가용성** - [가용성 테스트](./monitor-web-app-availability.md)의 결과입니다.

## <a name="filter-on-property-values"></a>속성 값에서 필터링

해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다. 필터 아이콘을 클릭하여 ![필터 아이콘](./media/diagnostic-search/filter-icon.png) 시작합니다.

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.

필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다.

## <a name="find-events-with-the-same-property"></a>동일한 속성이 있는 이벤트 찾기

동일한 속성 값을 가진 모든 항목을 찾으려면 검색 창에 입력하거나 필터 탭에서 속성을 볼 때 확인란을 클릭합니다.

![필터 탭에서 속성의 확인란을 클릭합니다.](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>데이터 검색

> [!NOTE]
> 더 복잡한 쿼리를 작성하려면 검색 블레이드 위쪽에서 [**로그(분석)**](../logs/log-analytics-tutorial.md)을 엽니다.
>

속성 값 중 하나에서 용어를 검색할 수 있습니다. 이는 속성 값을 포함하는 [사용자 지정 이벤트](./api-custom-events-metrics.md)를 작성한 경우에 유용합니다.

더 짧은 시간 범위로 검색하는 것이 더 빠른 것처럼 시간 범위를 설정하고자 할 수 있습니다.

![진단 검색 열기](./media/diagnostic-search/search-property.png)

하위 문자열이 아닌 전체 단어를 검색합니다. 인용 부호를 사용하여 특수 문자를 묶습니다.

| String | 찾을 수 *없음* | 있음 |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|미국|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

다음은 사용할 수 있는 검색 식입니다.

| 샘플 쿼리 | 영향 |
| --- | --- |
| `apple` |지정된 시간 범위의 필드에 단어 "apple"이 포함된 모든 이벤트를 찾습니다. |
| `apple AND banana` <br/>`apple banana` |두 단어를 모두 포함하는 이벤트를 찾습니다. "and"가 아닌 대문자 "AND"를 사용하세요. <br/>약식입니다. |
| `apple OR banana` |둘 중 한 단어를 포함하는 이벤트를 찾습니다. "or"가 아닌 "OR"을 사용하세요. |
| `apple NOT banana` |한 단어는 포함하지만 다른 단어는 포함하지 않는 이벤트를 찾습니다. |

## <a name="sampling"></a>샘플링

앱에서 다량의 원격 분석을 생성하는 경우(및 ASP.NET SDK 버전 2.0.0-beta3 이상을 사용하는 경우), 적응 샘플링 모듈 이벤트의 대표적인 일부만 전송하여 포털에 전송되는 볼륨이 자동으로 줄어듭니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다.

[샘플링에 대해 알아봅니다](./sampling.md).

## <a name="create-work-item"></a>작업 항목 만들기

원격 분석 항목의 세부 정보를 이용하여 GitHub 또는 Azure DevOps에서 버그를 만들 수 있습니다.

원격 분석 항목을 클릭하여 엔드투엔드 트랜잭션 세부 정보 보기로 이동한 다음 **작업 항목 만들기** 를 선택합니다.

![새 작업 항목을 클릭하고, 필드를 편집한 다음 확인을 클릭합니다.](./media/diagnostic-search/work-item.png)

처음 이 작업을 할 때는 Azure DevOps 조직과 프로젝트 링크를 구성하라는 메시지가 나타납니다.

(작업 항목 탭에서 링크를 구성할 수도 있습니다.)

## <a name="send-more-telemetry-to-application-insights"></a>Application Insights에 더 많은 원격 분석 전송

Application Insights SDK에서 보낸 기본 원격 분석 외에도 다음을 수행할 수 있습니다.

* [.NET](./asp-net-trace-logs.md) 또는 [Java](java-2x-trace-logs.md)의 즐겨찾는 로깅 프레임워크에서 로그 추적을 캡처합니다. 이는 로그 추적을 통해 검색하고 페이지 보기, 예외 사항 및 기타 이벤트와 상관 관계를 지정할 수 있음을 의미합니다.
* [코드를 작성](./api-custom-events-metrics.md)하여 사용자 지정 이벤트, 페이지 보기 및 예외 사항을 보냅니다.

[Application Insights에 로그 및 사용자 지정 원격 분석을 보내는 방법에 대해 알아봅니다](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>질문 및 답변

### <a name="how-much-data-is-retained"></a><a name="limits"></a>얼마나 많은 데이터가 보존되나요?

[제한 요약](./pricing.md#limits-summary)을 참조하세요.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>내 서버 요청에서 게시 데이터를 어떻게 볼 수 있나요?

게시 데이터를 자동으로 기록하지는 않지만 [TrackTrace 또는 로그 호출](./asp-net-trace-logs.md)을 사용할 수 있습니다. 메시지 매개 변수에 게시 데이터를 넣습니다. 속성을 필터링할 수 있는 동일한 방법으로 메시지에서 필터링할 수는 없지만 크기 제안은 더 깁니다.

## <a name="next-steps"></a><a name="add"></a>다음 단계

* [분석에서 복잡한 쿼리 작성](../logs/log-analytics-tutorial.md)
* [Application Insights에 로그 및 사용자 지정 원격 분석 전송](./asp-net-trace-logs.md)
* [가용성 및 응답성 테스트 설정](./monitor-web-app-availability.md)
* [문제 해결](../faq.yml)
