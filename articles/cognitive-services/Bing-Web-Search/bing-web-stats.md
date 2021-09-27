---
title: Bing Web Search API에 대한 분석 추가
titleSuffix: Azure Cognitive Services
description: Bing Statistics는 Bing Image Search API에 대한 분석을 제공합니다. 분석에는 호출량, 주요 쿼리 문자열, 지리적 분포 등이 포함됩니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.custom: seodec2018
ms.openlocfilehash: 878a6fd49a46b47c3243b168469a15909102a019
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587065"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Bing Search API에 대한 분석 추가

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Statistics는 Bing Search API에 대한 분석을 제공합니다. 분석에는 호출량, 주요 쿼리 문자열, 지리적 분포 등이 포함됩니다. Azure 리소스로 이동하고 **Bing 통계 사용** 을 클릭하여 [Azure Portal](https://ms.portal.azure.com)에서 Bing 통계를 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> * 무료 `F0` 가격 책정 계층의 리소스로 Bing 통계를 사용할 수 없습니다.
> * 제3자에게 배포할 애플리케이션을 만들 때는 Bing Statistics 대시보드를 통해 사용 가능한 어떤 데이터도 사용할 수 없습니다.
> * Bing Statistics를 사용하도록 설정하면 구독 속도가 약간 늘어납니다. 자세한 내용은 [가격 책정](https://aka.ms/bingstatisticspricing)을 참조하세요.


다음 이미지는 각 Bing Search API 엔드포인트에 대해 사용 가능한 분석을 보여 줍니다.

![엔드포인트 지원 매트릭스별 배포](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>분석 액세스

Bing은 24시간마다 분석 데이터를 업데이트하고 [분석 대시보드](https://bingapistatistics.com)에서 액세스할 수 있는 최대 13개월 동안의 기록을 유지 관리합니다. Bing 통계에 등록하는 데 사용한 것과 동일한 MSA(Microsoft 계정)를 사용하여 로그인했는지 확인합니다.

> [!NOTE]  
> * 대시보드에 메트릭이 표시되는 데 최대 24시간이 걸릴 수 있습니다. 대시보드에는 데이터가 마지막으로 업데이트된 날짜 및 시간이 표시됩니다.  
> * 메트릭은 Bing Statistics 추가 기능을 사용하도록 설정할 때부터 사용할 수 있습니다.

## <a name="filter-the-data"></a>데이터 필터링

기본적으로 차트와 그래프는 사용자가 액세스할 수 있는 모든 메트릭 및 데이터를 표시합니다. 관심 있는 리소스, 지역/국가, 엔드포인트 및 보고 기간을 선택하여 차트 및 그래프에 표시되는 데이터를 필터링할 수 있습니다. 다음 필터를 변경할 수 있습니다.

- **리소스 ID**: Azure 구독을 식별하는 고유한 리소스 ID입니다. 둘 이상의 Bing Search API 계층을 구독하는 경우 이 목록에는 여러 ID가 포함됩니다. 기본적으로 모든 리소스가 선택됩니다.  
  
- **지역/국가**: 결과가 나오는 지역/국가입니다. 예를 들면 en-us(영어, 미국)와 같습니다. 기본적으로 모든 지역/국가가 선택됩니다. `en-WW` 시장은 호출에 시장이 지정되지 않고 Bing에서 사용자의 시장을 확인할 수 없는 경우 Bing에서 사용되는 시장입니다.  
  
- **엔드포인트**: Bing Search API 엔드포인트입니다. 목록에는 유료 구독이 있는 모든 엔드포인트가 포함됩니다. 기본적으로 모든 엔드포인트가 선택됩니다.  

- **시간 프레임**: 보고 기간입니다. 다음을 지정할 수 있습니다.
  - **모두**: 최대 13개월 동안의 데이터가 포함됩니다.  
  - **지난 24시간**: 지난 24시간 동안의 분석이 포함됩니다.  
  - **지난 주**: 이전 7일의 분석이 포함됩니다.  
  - **지난 달**: 이전 30일의 분석이 포함됩니다.  
  - **사용자 지정 날짜 범위**: 사용 가능한 경우 지정된 날짜 범위에서 분석이 포함됩니다.  

## <a name="charts-and-graphs"></a>차트 및 그래프

대시보드에는 선택한 엔드포인트에 사용할 수 있는 메트릭의 차트 및 그래프가 표시됩니다. 모든 엔드포인트에 모든 메트릭을 사용할 수 있는 것은 아닙니다. 각 엔드포인트의 차트와 그래프는 정적입니다(표시할 차트 및 그래프를 선택할 수 없음). 대시보드에는 데이터가 있는 차트 및 그래프만 표시됩니다.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

가능한 메트릭 및 엔드포인트 제한 사항은 다음과 같습니다.

- **호출량**: 보고 기간 동안 수행된 호출 수를 표시합니다. 보고 기간이 하루 동안인 경우 차트에는 시간당 호출 수가 표시됩니다. 그렇지 않은 경우, 차트에는 보고 기간 중 하루당 호출 수가 표시됩니다.  
  
  > [!NOTE]
  > 호출량은 청구 보고서마다 다를 수 있으며 일반적으로는 성공한 호출만 포함됩니다.

- **상위 쿼리**: 보고 기간 동안의 상위 쿼리 및 각 쿼리의 발생 수가 표시됩니다. 표시된 쿼리의 수를 구성할 수 있습니다. 예를 들어, 상위 25, 50 또는 75개 쿼리를 표시할 수 있습니다. 다음 엔드포인트에 대해서는 상위 쿼리를 사용할 수 없습니다.  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 일부 쿼리 용어는 전자 메일, 전화 번호, SSN 등의 기밀 정보를 제거하도록 표시되지 않을 수 있습니다.

- **지리적 분포**: 검색 결과가 나오는 시장입니다. 예를 들어, `en-us`(영어, 미국) 등이 있습니다. Bing에서는 `mkt` 쿼리 매개 변수를 사용하여 지역/국가(지정된 경우)를 결정합니다. 그렇지 않은 경우, Bing은 호출자의 IP 주소와 같은 신호를 사용하여 지역/국가를 결정합니다.

- **응답 코드 배포**: 보고 기간 동안 수행된 모든 호출에 대한 HTTP 상태 코드입니다.

- **호출 원본 배포**: 사용자가 사용하는 브라우저 유형입니다. Microsoft Edge, Chrome, Safari, FireFox 등을 예로 들 수 있습니다. 봇, Postman과 같은 외부 브라우저나 콘솔 앱의 curl을 사용하여 수행되는 호출은 라이브러리 아래에 그룹화됩니다. 원본은 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 원본을 파생하려고 시도합니다.  

- **안전 검색 배포**: 안전 검색 값의 배포입니다. 예를 들면 off, moderate 또는 strict입니다. `safeSearch` 쿼리 매개 변수는 값(지정된 경우)을 포함합니다. 이렇게 하지 않으면 Bing은 기본적으로 moderate 값을 사용합니다.  

- **요청된 답변 배포**: `responseFilter` 쿼리 매개 변수에서 사용자가 요청한 Web Search API 답변입니다.  

- **반환된 답변 배포**: Web Search API가 응답에 반환한 답변입니다.

- **응답 서버 배포**: API 요청을 처리하는 애플리케이션 서버입니다. 가능한 값은 Bing.com(데스크톱 및 랩톱 디바이스에서 제공된 트래픽) 및 Bing.com-mobile(모바일 디바이스에서 제공된 트래픽)입니다. 서버는 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 서버를 파생하려고 시도합니다.

## <a name="next-steps"></a>다음 단계

* [Bing Search API란?](bing-api-comparison.md)
* [Bing Search API 사용 및 표시 요구 사항](use-display-requirements.md)