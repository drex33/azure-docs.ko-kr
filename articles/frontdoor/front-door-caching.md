---
title: Azure Front Door - 캐싱 | Microsoft Docs
description: 이 문서는 캐싱을 사용한 회람 규칙을 사용하는 Front Door의 동작을 이해하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: duau
ms.openlocfilehash: 5dcc4c893be44c9ebb05139118e7936bbdf5b41d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223560"
---
# <a name="caching-with-azure-front-door"></a>Azure Front Door를 사용한 캐싱
다음 문서는 캐싱을 사용한 회람 규칙을 사용하는 Front Door의 동작을 설명합니다. Front Door는 동적 사이트 가속 및 부하 분산을 지원하는 최신 CDN(Content Delivery Network)이며 다른 CDN과 마찬가지로 캐싱 동작도 지원합니다.

## <a name="delivery-of-large-files"></a>대용량 파일 전달
Azure Front Door는 파일 크기의 제한 없이 대용량 파일을 전달합니다. Front Door는 개체 청크라는 기술을 사용합니다. 대용량 파일이 요청되면 Front Door는 백엔드에서 더 작은 파일 조각을 검색합니다. 전체 또는 바이트 범위의 파일을 요청받은 후 Front Door 환경은 백 엔드에서 8MB 청크의 파일을 요청합니다.

청크가 Front Door 환경에 도착하면 캐시되고 사용자에게 즉시 제공됩니다. 그런 다음 Front Door가 다음 청크를 동시에 프리페치합니다. 프리페치 과정 덕분에 콘텐츠가 사용자보다 한 청크 앞서 진행되므로 대기 시간이 줄어듭니다. 이 프로세스는 전체 파일을 다운로드(요청된 경우)하거나, 클라이언트에서 연결을 닫을 때까지 계속됩니다.

바이트 범위 요청에 대한 자세한 내용은 [RFC 7233](https://www.rfc-editor.org/info/rfc7233)을 읽어보세요.
Front Door는 수신되는 모든 청크를 캐시하므로 전체 파일을 Front Door 캐시에 캐시할 필요가 없습니다. 파일 또는 바이트 범위에 대한 요청은 캐시에서 처리됩니다. 청크가 모두 캐시되지 않은 경우 프리페치를 사용하여 백 엔드에서 청크를 요청합니다. 이 최적화는 바이트 범위 요청을 지원하는 백 엔드의 기능을 사용합니다. 백 엔드에서 바이트 범위 요청을 지원하지 않으면 이 최적화가 적용되지 않습니다.

## <a name="file-compression"></a>파일 압축
Front Door는 콘텐츠를 에지에서 동적으로 압축하므로 클라이언트에 보다 짧고 빠른 응답 시간을 제공할 수 있습니다. 파일을 압축할 수 있으려면 캐싱을 사용해야 하며 파일이 압축에 적합한 MIME 형식이어야 합니다. 현재 Front Door에서는 다음 목록의 변경을 허용하지 않습니다. 현재 목록:
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

또한 파일의 크기는 1KB 이상 8MB 이하여야 합니다.

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
- [Gzip(GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

요청이 gzip 및 Brotli 압축을 지원하는 경우 Brotli 압축이 우선적으로 적용됩니다.</br>
자산에 대한 요청이 압축을 지정하고 요청의 결과로 캐시 누락이 발생한 경우, Front Door는 POP 서버에서 직접 자산을 압축합니다. 이후 압축된 파일은 캐시에서 제공됩니다. 결과 항목은 transfer-encoding: chunked를 사용하여 반환됩니다.

> [!NOTE]
> 범위 요청은 다른 크기로 압축될 수 있습니다. Azure Front Door GET HTTP 요청에 대해 콘텐츠 길이 값이 동일해야 합니다. 클라이언트가 헤더를 사용하여 바이트 범위 요청을 보내면 `accept-encoding` 원본이 다른 콘텐츠 길이로 응답하면 Azure Front Door 503 오류를 반환합니다. 원본/Azure Front Door 압축을 사용하지 않도록 설정하거나 바이트 범위 요청에 대한 요청에서 제거할 규칙 집합 규칙을 만들 수 `accept-encoding` 있습니다.

## <a name="query-string-behavior"></a>쿼리 문자열 동작
Front Door를 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예들 들어 `http://www.contoso.com/content.mov?field1=value1&field2=value2`입니다. 요청의 쿼리 문자열에 키-값 쌍이 둘 이상인 경우 해당 순서는 중요하지 않습니다.
- **쿼리 문자열 무시**: 해당 모드에서는 Front Door가 첫 번째 요청에서 요청자의 쿼리 문자열을 백 엔드에 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 Front Door 환경에서 제공되는 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.

- **각 고유한 URL 캐시**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 `www.example.ashx?q=test1` 요청에 대한 백 엔드의 응답은 Front Door 환경에서 캐시되고, 동일한 쿼리 문자열로 후속 캐시에 대해 반환됩니다. `www.example.ashx?q=test2`에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.

## <a name="cache-purge"></a>캐시 제거

Front Door는 자산의 TTL(Time-to-Live)이 만료될 때까지 자산을 캐시합니다. 클라이언트가 만료된 TTL을 사용하여 자산을 요청할 때마다 Front Door 환경에서는 요청을 처리하기 위해 자산의 업데이트된 새 복사본이 있는지 검색한 후 새로 고친 캐시를 저장합니다.

사용자가 항상 자산의 최신 복사본을 받도록 보장하는 가장 좋은 방법은 각 업데이트에 대해 자산 버전 관리를 수행하여 자산을 새 URL로 게시하는 것입니다. Front Door는 다음 클라이언트 요청에 대한 새 자산을 즉시 검색합니다. 경우에 따라 모든 가장자리 노드에서 캐시된 콘텐츠를 제거하고 이러한 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다. 웹 애플리케이션에 대한 업데이트 또는 잘못된 정보가 포함된 신속한 업데이트 자산 때문일 수 있습니다.

에지 노드에서 제거하고자 하는 자산을 선택합니다. 모든 자산을 삭제하려면 **모두 제거** 를 선택합니다. 아니면 제거하고 싶은 각 자산의 경로를 **경로** 에 입력합니다.

제거할 경로 목록에서 지원되는 형식은 다음과 같습니다.

- **단일 경로 제거**: /pictures/strasbourg.png와 같은 파일 확장명을 사용하여 자산의 전체 경로(프로토콜 및 도메인 제외)를 지정하여 개별 자산을 제거합니다.
- **와일드 카드 제거**: 별표(\*)를 와일드 카드로 사용할 수 있습니다. 경로에 /\*가 포함된 엔드포인트의 모든 폴더, 하위 폴더, 파일을 제거하거나 폴더를 지정하고 맨 뒤에 /\*를 붙여(예: /pictures/\*) 특정 폴더의 모든 하위 폴더와 파일을 제거합니다.
- **루트 도메인 제거**: 경로에 "/"가 포함된 엔드포인트의 루트를 제거합니다.

> [!NOTE]
> **와일드카드 도메인 제거**: 이 섹션에서 설명한 대로 제거를 위해 캐시된 경로를 지정해도 Front Door에 연결된 와일드카드 도메인에는 적용되지 않습니다. 현재로서 와일드카드 도메인을 직접 제거하는 것은 지원되지 않습니다. 특정 하위 도메인과 제거 경로를 지정함으로써 특정 하위 도메인에서 경로를 제거할 수 있습니다. 예를 들어 내 Front Door에 `*.contoso.com`가 있는 경우, `foo.contoso.com/path/*`을 입력하여 내 하위 도메인 `foo.contoso.com`의 자산을 제거할 수 있습니다. 현재 제거 콘텐츠 경로에서 호스트 이름 지정은 해당하는 경우 와일드카드 도메인의 하위 도메인으로 제한됩니다.
>

Front Door의 캐시 제거에서는 대/소문자를 구분하지 않습니다. 또한 쿼리 문자열에 구애받지 않으므로 URL을 삭제하면 해당 URL의 모든 쿼리 문자열 변형이 삭제됩니다. 

## <a name="cache-expiration"></a>캐시 만료
다음 순서의 헤더를 사용하여 항목이 캐시에 저장될 기간을 결정합니다.</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. 만료: \<http-date>

Cache-Control: private, Cache-Control: no-cache, Cache-Control: no-store와 같이 응답이 캐시되지 않음을 나타내는 Cache-Control 응답 헤더가 적용됩니다.  Cache-Control이 없는 경우 기본 동작은 Front Door가 X 기간 동안 리소스를 캐시하는 것입니다. 여기서 X는 1~3일 사이에 임의로 선택됩니다.

## <a name="request-headers"></a>요청 헤더

캐싱을 사용하는 경우 다음 요청 헤더는 백 엔드로 전달되지 않습니다.
- Content-Length
- Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>캐시 동작 및 기간

캐시 동작 및 기간은 Front Door 디자이너 라우팅 규칙과 규칙 엔진 모두에서 구성할 수 있습니다. 규칙 엔진 캐싱 구성은 항상 Front Door 디자이너 라우팅 규칙 구성을 재정의합니다.

* *캐싱을* 사용하지 않도록 **설정하면** 원본 응답 지시문에 관계없이 Front Door 응답 콘텐츠를 캐시하지 않습니다.

* *캐싱을* **사용하는** 경우 캐시 동작은 캐시 기본 *기간 사용* 의 값에 따라 다릅니다.
    * *캐시 기본 기간 사용* 을 **예** 로 설정하면 Front Door 항상 원본 응답 헤더 지시문을 사용합니다. origin 지시문이 없으면 Front Door 1~3일 동안 콘텐츠를 캐시합니다.
    * *캐시 기본 기간 사용이* **아니요로** 설정된 경우 Front Door 항상 *캐시 기간(필수* 필드)으로 재정의됩니다. 즉, 원본 응답 지시문의 값을 무시하고 캐시 기간 동안 콘텐츠를 캐시합니다. 

> [!NOTE]
> * Front Door 디자이너 라우팅 규칙에 설정된 *캐시 기간은* **최소 캐시 기간** 입니다. 원본의 캐시 제어 헤더에 재정의 값보다 더 큰 TTL이 있는 경우 이 재정의가 작동하지 않습니다.
> * Azure Front Door 개체가 캐시에 저장되는 최소 시간을 보장하지 않습니다. 캐시된 콘텐츠는 더 자주 요청되는 콘텐츠를 위한 공간을 확보하기 위해 콘텐츠를 자주 요청하지 않는 경우 만료되기 전에 에지 캐시에서 제거될 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
