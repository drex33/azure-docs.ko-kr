---
title: 'Azure Front Door: 캐싱'
description: 이 문서는 캐싱이 사용하도록 설정된 회람 규칙을 사용하여 Azure Front Door 표준/프리미엄의 동작을 이해하는 데 도움이 됩니다.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: de2893b11c1104dece4622c6f3d952e43574b3fb
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031472"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)을 사용하여 캐싱

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 캐싱이 사용하도록 설정된 경우 Front Door 표준/프리미엄(미리 보기) 경로 및 규칙 집합이 동작하는 방법을 알아보겠습니다. Azure Front Door는 동적 사이트 가속 및 부하 분산 기능이 있는 최신 CDN(콘텐츠 배달 네트워크)입니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="request-methods"></a>요청 메서드

오직 GET 요청 메서드만이 Azure Front Door에서 캐시된 콘텐츠를 생성할 수 있습니다. 다른 모든 요청 메서드는 항상 네트워크를 통해 프록시됩니다.

## <a name="delivery-of-large-files"></a>대용량 파일 전달

Azure Front Door 표준/프리미엄(미리 보기)에서는 파일 크기의 제한 없이 대용량 파일을 전달합니다. Front Door는 개체 청크라는 기술을 사용합니다. 대용량 파일이 요청되면 Front Door가 원본에서 파일의 더 작은 파일 조각을 검색합니다. 전체 또는 바이트 범위의 파일 요청을 받으면 Front Door 환경은 원본에서 8MB 청크의 파일을 요청합니다.

청크가 Front Door 환경에 도착하면 캐시되고 사용자에게 즉시 제공됩니다. 그런 다음 Front Door가 다음 청크를 동시에 프리페치합니다. 프리페치 과정 덕분에 콘텐츠가 사용자보다 한 청크 앞서 진행되므로 대기 시간이 줄어듭니다. 이 프로세스는 전체 파일을 다운로드(요청된 경우)하거나, 클라이언트에서 연결을 닫을 때까지 계속됩니다.

바이트 범위 요청에 대한 자세한 내용은 [RFC 7233](https://www.rfc-editor.org/info/rfc7233)을 읽어보세요.
Front Door는 수신되는 모든 청크를 캐시하므로 전체 파일을 Front Door 캐시에 캐시할 필요가 없습니다. 파일 또는 바이트 범위에 대한 요청은 캐시에서 처리됩니다. 청크가 모두 캐시되지 않은 경우 프리페치를 사용하여 백엔드에서 청크를 요청합니다. 이 최적화는 바이트 범위 요청을 지원하는 원본의 기능을 사용합니다. 원본에서 바이트 범위 요청을 지원하지 않으면 이 최적화가 적용되지 않습니다.

## <a name="file-compression"></a>파일 압축

Azure 전면 도어에서 [파일을 압축 하 여 성능 향상](how-to-compression.md) 을 참조 하세요.

## <a name="query-string-behavior"></a>쿼리 문자열 동작

Front Door를 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예들 들어 `http://www.contoso.com/content.mov?field1=value1&field2=value2`입니다. 요청의 쿼리 문자열에 키-값 쌍이 둘 이상인 경우 해당 순서는 중요하지 않습니다.

* **쿼리 문자열 무시**: 이 모드에서는 Front Door가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본으로 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 Front Door 환경에서 제공되는 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.

* **각 고유한 URL 캐시**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 `www.example.ashx?q=test1` 요청에 대한 원본에서의 응답이 Front Door 환경에서 캐시되고, 쿼리 문자열이 이와 동일한 후속 캐시에 대해 반환됩니다. `www.example.ashx?q=test2`에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.
* 또한 **캐시 키 쿼리 문자열** 동작을 지정하는 규칙 집합을 사용하여 캐시 키가 생성될 때 지정된 매개 변수를 포함하거나 제외할 수 있습니다. 예를 들어, 기본 캐시 키는 /foo/image/asset.html이고 샘플 요청은 `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200`입니다. 쿼리 문자열 'userid'를 제외하는 규칙 집합 규칙이 있습니다. 그러면 쿼리 문자열 캐시 키가 `/foo/image/asset.html?language=EN&sessionid=200`이(가) 됩니다.

## <a name="cache-purge"></a>캐시 제거

캐시 제거를 참조하세요.

## <a name="cache-expiration"></a>캐시 만료
다음 순서의 헤더를 사용하여 항목이 캐시에 저장될 기간을 결정합니다.</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. 만료: \<http-date>

Cache-Control: private, Cache-Control: no-cache, Cache-Control: no-store와 같이 응답이 캐시되지 않음을 나타내는 Cache-Control 응답 헤더가 적용됩니다.  Cache-Control 없는 경우, 기본 동작은 Front Door가 X시간 동안 리소스를 캐시하는 것입니다. 여기서 X는 1일에서 3일 중에서 임의로 선택됩니다.

## <a name="request-headers"></a>요청 헤더

캐싱을 사용하는 경우 다음 요청 헤더는 원본으로 전달되지 않습니다.
* Content-Length
* Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>캐시 동작 및 지속 시간

프런트 도어 디자이너 라우팅 규칙과 규칙 엔진 모두에서 캐시 동작 및 기간을 구성할 수 있습니다. 규칙 엔진 캐싱 구성은 항상 Front 도어 디자이너 라우팅 규칙 구성을 재정의 합니다.

* *캐싱이* **사용 하지 않도록 설정** 된 경우 전면 도어는 원본 응답 지시문에 관계 없이 응답 콘텐츠를 캐시 하지 않습니다.

* *캐싱이* **설정** 된 경우 캐시의 *기본 지속 기간* 값 마다 캐시 동작이 다릅니다.
    * *사용 캐시 기본 지속 기간* 을 **예** 로 설정 하면 Front 도어가 항상 원본 응답 헤더 지시어를 인식 합니다. 원본 지시어가 없는 경우 전면 도어는 1 일에서 3 일 사이에 콘텐츠를 캐시 합니다.
    * *사용 캐시 기본 지속 기간* 을 **아니요** 로 설정 하면 프런트 도어는 항상 *캐시 기간* (필수 필드)으로 재정의 됩니다. 즉, 원본 응답 지시문의 값을 무시 하 고 캐시 기간에 대 한 콘텐츠를 캐시 합니다. 

> [!NOTE]
> * 전면 도어 디자이너 라우팅 규칙에 설정 된 *캐시 기간이* **최소 캐시 기간** 입니다. 원본에서 캐시 컨트롤 헤더의 TTL이 재정의 값 보다 크면이 재정의는 작동 하지 않습니다.
> * Azure 전면 도어를 사용 하면 콘텐츠가 캐시에 저장 되는 시간을 보장할 수 없습니다. 콘텐츠가 자주 사용 되지 않는 경우 콘텐츠가 만료 되기 전에 캐시 된 콘텐츠가에 지 캐시에서 제거 될 수 있습니다. 캐시 된 데이터가 만료 된 경우에도 Front 도어가 캐시의 데이터를 처리할 수 있습니다. 이 동작은 원본이 오프 라인일 때 사이트를 부분적으로 사용 가능 하 게 유지 하는 데 도움이 될 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

* [규칙 집합 일치 조건](concept-rule-set-match-conditions.md)에 대해 자세히 알아보기
* [규칙 집합 작업](concept-rule-set-actions.md)에 대해 자세히 알아보기
