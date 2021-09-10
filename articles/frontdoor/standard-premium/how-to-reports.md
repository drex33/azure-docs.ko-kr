---
title: Azure Front Door 표준/프리미엄(미리 보기) 보고서
description: 이 문서에서는 Azure Front Door에서 보고가 작동하는 방식을 설명합니다.
services: frontdoor
author: jessie-jyy
ms.service: frontdoor
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: yuajia
ms.openlocfilehash: 8e43bf01be44c8d82bb8b5f9d466552fc9d7ad17
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595006"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure Front Door 표준/프리미엄(미리 보기) 보고서

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Front Door 표준/프리미엄 분석 보고서는 Azure Front Door가 관련된 Web Application Firewall 메트릭과 함께 작동하는 방법에 대한 기본 제공 및 전체 보기를 제공합니다. 액세스 로그를 활용하여 추가 문제 해결 및 디버깅을 수행할 수도 있습니다. Azure Front Door 분석 보고서에는 트래픽 보고서와 보안 보고서가 포함됩니다.

| 보고서 | 세부 정보 |
|---------|---------|
| 주요 메트릭 개요 | Azure Front Door 에지에서 클라이언트로 전송된 전체 데이터를 표시합니다.<br/>- 최고 대역폭<br/>- 요청 수 <br/>- 캐시 적중률<br/> - 총 대기 시간<br/>- 5XX 오류율 |
| 도메인별 트래픽 | - 프로필 아래의 모든 도메인에 대한 개요를 제공<br/>- AFD 에지에서 클라이언트로 발신된 데이터의 분석<br/>- 총 요청 수<br/>- 도메인별 3XX/4XX/5XX 응답 코드 |
| 위치별 트래픽 | - 상위 국가/지역의 요청 및 사용에 대한 맵 보기를 표시<br/>- 상위 국가/지역의 추세 보기 |
| 사용량 | - Azure Front Door 에지에서 클라이언트로의 데이터 발신을 표시<br/>- 원본에서 AFD 에지로의 데이터 발신<br/>- AFD 에지에서 클라이언트로의 대역폭<br/>- 원본에서 AFD 에지로의 대역폭<br/>- 요청 수<br/>- 총 대기 시간<br/>- HTTP 상태 코드별 요청 수 추세 |
| 캐싱 | - 요청 수별 캐시 적중률을 표시<br/>- 적중 요청 및 누락 요청의 추세 보기 |
| 상위 URL | - 요청 수를 표시 <br/>- 전송된 데이터 <br/>- 캐시 적중률 <br/>- 가장 많이 요청된 50개 자산의 응답 상태 코드 분포 |
| 상위 참조 페이지 | - 요청 수를 표시 <br/>- 전송된 데이터 <br/>- 캐시 적중률 <br/>- 트래픽을 생성하는 상위 50개 참조 페이지의 응답 상태 코드 분포 |
| 상위 사용자 에이전트 | - 요청 수를 표시 <br/>- 전송된 데이터 <br/>- 캐시 적중률 <br/>- 콘텐츠를 요청하는 데 사용된 상위 50개 사용자 에이전트의 응답 상태 코드 분포 |

| 보안 보고서 | 세부 정보 |
|---------|---------|
| 주요 메트릭 개요 | - 일치하는 WAF 규칙을 표시<br/>- 일치하는 OWASP 규칙<br/>- 일치하는 봇 규칙<br/>- 일치하는 사용자 지정 규칙 |
| 차원별 메트릭 | - 작업별 일치하는 WAF 규칙 추세의 분석<br/>- 규칙 집합 유형별 이벤트 및 규칙 그룹별 이벤트의 도넛형 차트<br/>- 규칙 ID, 국가/지역, IP 주소, URL 및 사용자 에이전트별 상위 이벤트의 분석 목록  |

> [!NOTE]
> 보안 보고서는 Azure Front Door 프리미엄 SKU에서만 사용할 수 있습니다.

대부분의 보고서는 액세스 로그를 기반으로 하며 Azure Front Door에서 고객에게 무료로 제공됩니다. 고객은 이러한 보고서를 보기 위해 액세스 로그를 사용하도록 설정하거나 구성할 필요가 없습니다. 보고서는 포털 및 API를 통해 액세스할 수 있습니다. CSV 다운로드도 지원됩니다. 

보고서는 이전 90일부터 선택한 날짜 범위를 지원합니다. 데이터 요소는 선택한 날짜 범위에 따라 5분, 매시간 또는 매일 간격입니다. 일반적으로는 1시간 이내의 지연으로 데이터를 볼 수 있지만 경우에 따라 최대 몇 시간까지 지연될 수도 있습니다. 

## <a name="access-reports-using-the-azure-portal"></a>Azure Portal을 사용하여 보고서 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Front Door 표준/프리미엄 프로필을 선택합니다.

1. 탐색 창의 분석 아래에서 **보고서 또는 보안** 을 선택합니다.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="보고서 방문 페이지의 스크린샷":::

1. 다양한 차원에 대한 7개 탭이 있습니다. 원하는 차원을 선택합니다.

   * 도메인별 트래픽
   * 사용량 
   * 위치별 트래픽
   * 캐시
   * 상위 URL
   * 상위 참조 페이지
   * 상위 사용자 에이전트

1. 차원을 선택한 후에 다양한 필터를 선택할 수 있습니다.
  
    1. **다음 기간의 데이터 표시** - 도메인별 트래픽을 확인할 날짜 범위를 선택합니다. 사용 가능한 범위는 다음과 같습니다.
        
        * 최근 24시간
        * 최근 7일
        * 지난 30일
        * 최근 90일
        * 이번 달
        * 지난달
        * 사용자 지정 날짜

         기본적으로 지난 7일 동안의 데이터가 표시됩니다. 꺾은선형 차트를 사용하는 탭의 경우 데이터 세분성은 기본 동작으로 선택한 날짜 범위에 따라 달라집니다. 
    
        * 5분 - 24시간 이내 날짜 범위에 대해 5분마다 데이터 요소 1개
        * 매시간 – 24시간부터 30일까지의 날짜 범위에 대해 1시간마다 데이터 요소 1개
        * 매일 – 30일을 초과하는 날짜 범위에 대해 매일 데이터 요소 1개

        항상 집계를 사용하여 기본 집계 세분성을 변경할 수 있습니다. 참고: 14일을 초과하는 데이터 범위에서는 5분이 작동하지 않습니다. 

    1. **위치** - 국가/지역별로 단일 또는 여러 클라이언트 위치를 선택합니다. 국가/지역은 북아메리카, 아시아, 유럽, 아프리카, 오세아니아 및 남아메리카의 6개 지역으로 그룹화됩니다. [국가/지역 매핑](https://en.wikipedia.org/wiki/Subregion)을 참조하세요. 기본적으로 모든 국가가 선택됩니다.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="위치 차원 보고서의 스크린샷":::
   
    1. **프로토콜** - 트래픽 데이터를 보려면 HTTP 또는 HTTPS 중 하나를 선택합니다.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="프로토콜 차원 보고서의 스크린샷":::
    
    1. **도메인** - 단일 또는 다중 엔드포인트 또는 사용자 지정 도메인을 선택합니다. 기본적으로 모든 엔드포인트와 사용자 지정 도메인이 선택됩니다. 
    
        * 한 프로필에서 엔드포인트 또는 사용자 지정 도메인을 삭제한 다음 다른 프로필에 동일한 엔드포인트 또는 도메인을 다시 만드는 경우 이 엔드포인트는 두 번째 엔드포인트로 간주됩니다.  
        * 사용자 지정 도메인을 기준으로 보고서를 보는 경우 사용자 지정 도메인 하나를 삭제하고 다른 엔드포인트에 바인딩하면 이러한 도메인은 하나의 사용자 지정 도메인으로 취급됩니다. 엔드포인트별로 보는 경우 엔드포인트는 별개 항목으로 취급됩니다. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="도메인 차원 보고서의 스크린샷":::

1. 데이터를 CSV 파일로 내보내려면 선택한 탭에서 CSV 다운로드 링크를 선택합니다.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="보고서용 CSV 파일 다운로드의 스크린샷":::

### <a name="key-metrics-for-all-reports"></a>모든 보고서에 대한 주요 메트릭

| 메트릭 | 설명 |
|---------|---------|
| 전송되는 데이터 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에 대해 AFD 에지 POP에서 클라이언트로 전송된 데이터를 표시합니다. |
| 최대 대역폭 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에서 Azure Front Door 에지 POP에서 클라이언트로의 최대 대역폭 사용량(bit/s)입니다. | 
| 총 요청 수 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에서 AFD 에지 POP가 클라이언트에 응답한 요청 수입니다. |
| 캐시 적중률 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에서 AFD가 에지 캐시로부터 콘텐츠를 제공한 모든 캐시 가능 요청의 백분율입니다. |
| 5XX 오류율 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에서 클라이언트에 대한 HTTP 상태 코드가 5XX인 요청의 백분율입니다. |
| 총 대기 시간 | 선택한 시간 프레임, 클라이언트 위치, 도메인 및 프로토콜에서 모든 요청의 평균 대기 시간입니다. 각 요청의 대기 시간은 Azure Front Door가 클라이언트 요청을 수신한 시점부터 Azure Front Door가 클라이언트로 마지막 응답 바이트를 전송한 시점까지의 총 시간으로 측정됩니다. |

## <a name="traffic-by-domain"></a>도메인별 트래픽

도메인별 트래픽은 이 Azure Front Door 프로필 아래의 모든 도메인에 대한 그리드 보기를 제공합니다. 이 보고서에서 다음을 확인할 수 있습니다. 
* 요청
* Azure Front Door에서 클라이언트로 발신된 데이터
* 각 도메인의 상태 코드(3XX, 4XX 및 5XX)가 있는 요청

도메인에는 보고서 액세스 세션에 설명된 대로 엔드포인트 및 사용자 지정 도메인이 포함됩니다.  

예상보다 저조한 메트릭이 있는 경우 다른 탭으로 이동하여 추가로 조사하거나 액세스 로그에서 자세한 내용을 확인할 수 있습니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="보고서 방문 페이지의 스크린샷":::


## <a name="usage"></a>사용량

이 보고서는 다음과 같은 다양한 차원을 기준으로 트래픽 및 응답 상태 코드의 추세를 보여 줍니다.

* 에지에서 클라이언트로 전송된 데이터 및 원본에서 에지로 전송된 데이터, 꺾은선형 차트 

* 프로토콜별 에지에서 클라이언트로 전송된 데이터, 꺾은선형 차트 

* 에지에서 클라이언트로의 요청 수, 꺾은선형 차트  

* 프로토콜별(HTTP 및 HTTPS) 에지에서 클라이언트로의 요청 수, 꺾은선형 차트 

* 에지에서 클라이언트로의 대역폭, 꺾은선형 차트 

* 총 대기 시간. Front Door가 클라이언트 요청을 수신한 시점부터 Front Door가 클라이언트로 마지막 응답 바이트를 전송한 시점까지의 총 시간을 측정합니다.

* HTTP 상태 코드별 에지에서 클라이언트로의 요청 수, 꺾은선형 차트. 모든 요청은 HTTP 상태 코드를 생성합니다. HTTP 상태 코드는 원시 로그의 HTTPStatusCode에 표시됩니다. 상태 코드는 CDN 에지가 요청을 처리한 방식을 설명합니다. 예를 들어 2xx 상태 코드는 클라이언트 요청이 성공적으로 처리되었음을 나타냅니다. 4xx 상태 코드는 오류가 발생했음을 나타냅니다. HTTP 상태 코드에 대한 자세한 내용은 HTTP 상태 코드 목록을 참조하세요. 

* HTTP 상태 코드별 에지에서 클라이언트로의 요청 수. 그리드 내 모든 요청의 HTTP 상태 코드별 요청 백분율입니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="사용량 보고서의 스크린샷" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>위치별 트래픽

이 보고서는 자산에 가장 많이 액세스하는 방문자의 국가/지역별 상위 50개 위치를 표시합니다. 또한 이 보고서는 국가/지역별 메트릭 분석을 제공하고 가장 많은 트래픽이 생성되는 상위 국가/지역에 대한 전체 보기를 제공합니다. 마지막으로 캐시 적중률 또는 4XX/5XX 오류 코드가 높은 국가/지역을 확인할 수 있습니다.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="위치별 보고서의 스크린샷" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

이 보고서에는 다음 항목이 포함됩니다.

* 데이터 발신량 또는 선택한 요청을 기준으로 상위 50개 국가/지역의 세계 지도 보기
* 데이터 발신량 또는 선택한 요청을 기준으로 상위 5개 국가/지역의 2선 차트 추세 보기 
* AFD에서 클라이언트로 해당 데이터가 발신된 상위 국가/지역의 표 - 전체 국가/지역 중 데이터 발신 비율(%), 요청 수, 전체 국가/지역 중 요청 비율(%), 캐시 적중률, 4XX 응답 코드 및 5XX 응답 코드

## <a name="caching"></a>캐싱

캐싱 보고서는 요청에 따라 캐시 적중/누락 및 캐시 적중률의 차트 보기를 제공합니다. 이러한 주요 메트릭은 CDN이 콘텐츠를 캐시하는 방식을 설명합니다. 가장 빠른 성능은 캐시 적중에서 비롯되기 때문입니다. 캐시 누락을 최소화하여 데이터 전달 속도를 최적화할 수 있습니다. 이 보고서에는 다음 항목이 포함됩니다.

* 캐시 적중 및 누락 수 추세, 꺾은선형 차트

* 캐시 적중률, 꺾은선형 차트

캐시 적중/누락 수는 캐시가 클라이언트 요청을 적중 또는 누락한 요청 수를 설명합니다.

* 적중: Azure CDN 에지 서버가 직접 처리한 클라이언트 요청입니다. 원시 로그의 CacheStatus 값이 HIT, PARTIAL_HIT, 또는 REMOTE HIT인 요청을 나타냅니다. 

* 누락: Azure CDN 에지 서버가 원본에서 콘텐츠를 가져와 처리한 클라이언트 요청입니다. 원시 로그의 CacheStatus 값이 MISS인 요청을 나타냅니다. 

**캐시 적중률** 은 에지가 직접 처리한 캐시된 요청의 백분율입니다. 캐시 적중률의 수식은 `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%`입니다. 

이 보고서는 캐싱 시나리오를 고려하며 다음 요구 사항을 충족하는 요청이 계산에 포함됩니다. 

* 요청한 콘텐츠가 요청자 또는 원본 보호에 가장 가까운 POP에 캐시됨 

* 개체 청크를 위한 부분적으로 캐시된 콘텐츠

다음과 같은 경우에는 모두 제외됩니다. 

* 규칙 집합으로 인해 거부된 요청

* 캐시 사용 안 함으로 설정된 일치 규칙 집합을 포함하는 요청 

* WAF에 의해 차단된 요청 

* 원본 응답 헤더는 캐시되지 않아야 함을 의미합니다. 예를 들어 Cache-Control: private, Cache-Control: no-cache 또는 Pragma: no-cache 헤더는 자산이 캐시되지 않도록 합니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="캐싱 보고서의 스크린샷":::

## <a name="top-urls"></a>상위 URL

상위 URL을 사용하면 특정 엔드포인트 또는 사용자 지정 도메인에서 발생하는 트래픽 양을 볼 수 있습니다. 지난 90일 이내의 모든 기간에서 가장 많이 요청된 50개 자산에 대한 데이터가 표시됩니다. 인기 있는 URL이 다음 값과 함께 표시됩니다. 사용자는 요청 수, 요청 %, 전송된 데이터 및 전송된 데이터 %를 기준으로 URL을 정렬할 수 있습니다. 모든 메트릭은 1시간 단위로 집계되며 선택한 시간 프레임마다 다를 수 있습니다. URL은 액세스 로그의 RequestUri 값을 참조합니다. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="상위 URL 보고서의 스크린샷":::

* URL, 요청된 자산의 전체 경로를 나타내며 형식은 `http(s)://contoso.com/index.html/images/example.jpg` 
* 요청 수 
* Azure Front Door가 처리한 총 요청에 대한 요청 % 
* 전송된 데이터 
* 전송된 데이터 % 
* 캐시 적중률 %
* 응답 코드가 4XX인 요청
* 응답 코드가 5XX인 요청

> [!NOTE]
> 상위 URL은 시간 경과에 따라 달라지므로 Azure Front Door는 상위 50개 URL의 정확한 목록을 가져오기 위해 모든 URL 요청 수를 매시간 계산하고 일중 누계를 유지합니다. 500개 URL의 하위권에 위치한 URL은 당일에도 순위가 상승하거나 목록에서 탈락할 수 있으므로 이러한 URL의 총 수는 근사치입니다.  
>
> 상위 50개 URL은 순위가 상승 또는 하락할 수 있지만 목록에서 탈락하는 경우는 드뭅니다. 따라서 상위 URL의 숫자는 일반적으로 신뢰할 수 있습니다. URL이 목록에서 탈락했다가 당일 다시 포함되는 경우 목록에서 탈락한 기간 동안의 요청 수는 해당 기간 동안 발생하는 URL의 요청 수를 기반으로 추정됩니다.  
>
> 상위 사용자 에이전트에도 동일한 논리가 적용됩니다. 

## <a name="top-referrers"></a>상위 참조 페이지

상위 참조 페이지를 통해 고객은 특정 엔드포인트 또는 사용자 지정 도메인의 콘텐츠에 대한 요청이 가장 많이 시작된 상위 50개 참조 페이지를 볼 수 있습니다. 지난 90일 이내의 모든 기간에 대한 데이터를 볼 수 있습니다. 참조 페이지는 요청이 생성된 URL을 나타냅니다. 참조 페이지는 검색 엔진 또는 다른 웹 사이트에서 나올 수 있습니다. 사용자가 브라우저의 주소창에 URL을 직접 입력하는 경우(예: http(s)://contoso.com/index.html) 요청의 참조 페이지는 '비어' 있습니다. 상위 참조 페이지 보고서에는 다음 값이 포함됩니다. 요청 수, 요청 %, 전송된 데이터 및 전송된 데이터 %를 기준으로 정렬할 수 있습니다. 모든 메트릭은 1시간 단위로 집계되며 선택한 시간 프레임마다 다를 수 있습니다. 

* 참조 페이지, 원시 로그의 Referrer 값 
* 요청 수 
* 선택한 기간 동안 Azure CDN이 처리한 총 요청에 대한 요청 %. 
* 전송되는 데이터 
* 전송된 데이터 % 
* 캐시 적중률 %
* 응답 코드가 4XX인 요청
* 응답 코드가 5XX인 요청

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="상위 참조 페이지 보고서의 스크린샷":::

## <a name="top-user-agent"></a>상위 사용자 에이전트

이 보고서에서는 콘텐츠를 요청하는 데 사용된 상위 50개 사용자 에이전트의 그래픽 및 통계 보기를 사용할 수 있습니다. 예제:
* Mozilla/5.0(Windows NT 10.0; WOW64) 
* AppleWebKit/537.36(KHTML, like Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

표에 요청 수, 요청 %, 전송된 데이터 및 전송된 데이터 %, 캐시 적중률 %, 응답 코드가 4XX인 요청 및 응답 코드가 5XX인 요청이 표시됩니다. 사용자 에이전트는 액세스 로그의 UserAgent 값을 참조합니다.

## <a name="security-report"></a>보안 보고서

이 보고서에서는 각 차원별로 WAF 패턴의 그래픽 및 통계 보기를 사용할 수 있습니다.

| 차원 | Description |
|---------|---------|
| 개요 메트릭 - 일치하는 WAF 규칙 | 사용자 지정 WAF 규칙, 관리형 WAF 규칙, 봇 관리자와 일치하는 요청입니다. |
| 개요 메트릭 - 차단된 요청 | WAF 규칙과 일치한 모든 요청 중 WAF 규칙에 의해 차단된 요청의 백분율입니다. |
| 개요 메트릭 - 일치하는 관리형 규칙 | 차단, 로그, 허용 및 리디렉션 요청의 추세에 대한 4개의 꺾은선형 차트입니다. |
| 개요 메트릭 - 일치하는 사용자 지정 규칙 | 사용자 지정 WAF 규칙과 일치하는 요청입니다. |
| 개요 메트릭 - 일치하는 봇 규칙 | 봇 관리자와 일치하는 요청입니다. |
| 작업별 WAF 요청 추세 | 차단, 로그, 허용 및 리디렉션 요청의 추세에 대한 4개의 꺾은선형 차트입니다. |
| 규칙 유형별 이벤트 | 규칙 유형별 WAF 요청 분포에 대한 도넛형 차트입니다(예: 봇, 사용자 지정 규칙 및 관리형 규칙). |
| 규칙 그룹별 이벤트 | 규칙 그룹별 WAF 요청 분포에 대한 도넛형 차트입니다. |
| 작업별 요청 | 작업별 요청 수에 대한 테이블입니다(내림차순). |
| 상위 규칙 ID별 요청 | 상위 50개 규칙 ID의 요청 수를 보여 주는 표입니다(내림차순). |
| 상위 국가/지역별 요청 |  상위 50개 국가/지역의 요청 수를 보여주는 표입니다(내림차순). |
| 상위 클라이언트 IP별 요청 |  상위 50개 IP의 요청 수를 보여 주는 표입니다(내림차순). |
| 상위 요청 URL별 요청 |  상위 50개 URL의 요청 수를 보여 주는 표입니다(내림차순). |
| 상위 호스트 이름별 요청 | 상위 50개 호스트 이름의 요청 수를 보여 주는 표입니다(내림차순). |
| 상위 사용자 에이전트별 요청 | 상위 50개 사용자 에이전트의 요청 수를 보여 주는 표입니다(내림차순). |

## <a name="cvs-format"></a>CVS 형식

보고서의 여러 탭에 대한 CSV 파일을 다운로드할 수 있습니다. 이 섹션에서는 각 CSV 파일의 값에 대해 설명합니다.

### <a name="general-information-about-the-cvs-report"></a>CVS 보고서에 대한 일반 정보

모든 CSV 보고서는 일반 정보를 포함하며, 이 정보는 모든 CSV 파일에서 사용할 수 있습니다. 다운로드한 보고서를 기반으로 하는 변수가 포함됩니다. 


| 값 | 설명 |
|---------|---------|
| 보고서 | 보고서의 이름 | 
| 도메인 | 보고서의 엔드포인트 또는 사용자 지정 도메인의 목록입니다. |
| StartDateUTC | 보고서를 생성한 날짜 범위의 시작 시간(UTC)입니다. |
| EndDateUTC | 보고서를 생성한 날짜 범위의 종료 시간(UTC)입니다. |
| GeneratedTimeUTC | 보고서를 생성한 날짜 및 시간(UTC)입니다. |
| Location | 클라이언트 요청이 시작된 국가/지역의 목록입니다. 기본값은 모두입니다. 보안 보고서에는 적용되지 않습니다. |
| 프로토콜 | 요청의 프로토콜입니다(HTTP 또는 HTTPs). 보고서 및 보안 보고서의 상위 URL 및 사용자 에이전트별 트래픽에는 적용되지 않습니다. |
| 집계 | 각 행의 데이터 집계 세분성입니다(5분, 매시간, 매일). 보고서 및 보안 보고서의 도메인별 트래픽, 상위 URL 및 사용자 에이전트별 트래픽에는 적용되지 않습니다. |

### <a name="data-in-traffic-by-domain"></a>도메인별 트래픽 데이터

* 도메인 
* 총 요청 
* 캐시 적중률 
* 3XX 요청 
* 4XX 요청 
* 5XX 요청 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>위치별 트래픽 데이터

* Location
* TotalRequests
* Request%
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>사용량 데이터

이 CSV 파일에는 세 가지 보고서가 있습니다. 각각 HTTP 프로토콜, HTTPS 프로토콜, HTTP 상태 코드에 대한 보고서입니다. 

HTTP 및 HTTPs에 대한 보고서는 동일한 데이터 세트를 공유합니다. 

* 시간 
* 프로토콜 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

HTTP 상태 코드에 대한 보고서입니다. 

* 시간 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>캐싱 데이터 

* 시간
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>상위 URL 데이터 

* URL 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>사용자 에이전트 데이터 

* UserAgent 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="security-report"></a>보안 보고서 

모두 아래 필드를 동일하게 포함하는 7개 표가 있습니다.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

7개 표는 시간, 규칙 ID, 국가/지역, IP 주소, URL, 호스트 이름, 사용자 에이전트에 대한 것입니다. 

## <a name="next-steps"></a>다음 단계

[Azure Front Door 표준/프리미엄 실시간 모니터링 메트릭](how-to-monitor-metrics.md)에 대해 알아봅니다.
