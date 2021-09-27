---
title: Bing Video Search API v5에서 v7로 업그레이드
titleSuffix: Azure Cognitive Services
description: Bing Video Search 애플리케이션에서 버전 7을 사용하도록 업데이트해야 하는 부분을 식별합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 9c76c2247e92a5ebc70797cdb6794eb727280e4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600726"
---
# <a name="video-search-api-upgrade-guide"></a>Video Search API 업그레이드 가이드

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 업그레이드 가이드는 Bing Video Search API의 버전 5와 버전 7 사이의 변경 내용을 식별합니다. 이 가이드를 사용하면 버전 7을 사용하도록 업데이트해야 하는 애플리케이션의 부분을 식별하는 데 유용합니다.

## <a name="breaking-changes"></a>주요 변경 내용

### <a name="endpoints"></a>엔드포인트

- 엔드포인트의 버전 번호가 v5에서 v7로 변경되었습니다. 예들 들어 `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`입니다.

### <a name="error-response-objects-and-error-codes"></a>오류 응답 개체 및 오류 코드

- 이제 모든 실패한 요청에 대한 응답 본문에 `ErrorResponse` 개체가 포함되어야 합니다.

- 다음 필드가 `Error` 개체에 추가되었습니다.  
  - `subCode`&mdash;가능한 경우 불연속 버킷으로 오류 코드 분할
  - `moreDetails`&mdash;`message` 필드에 설명된 오류에 대한 추가 정보
   

- v5 오류 코드가 다음과 같이 가능한 `code` 및 `subCode` 값으로 바뀌었습니다.

|코드|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing은 하위 코드 조건 중 하나가 발생할 때마다 ServerError를 반환합니다. 응답은 HTTP 상태 코드가 500인 경우 이러한 오류를 포함합니다.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>차단|Bing은 요청의 일부가 잘못된 경우 항상 InvalidRequest를 반환합니다. 예를 들어 필수 매개 변수가 없거나 매개 변수 값이 잘못된 경우입니다.<br/><br/>오류가 ParameterMissing 또는 ParameterInvalidValue이면 HTTP 상태 코드는 400입니다.<br/><br/>오류가 HttpNotAllowed이면 HTTP 상태 코드는 410입니다.
|RateLimitExceeded||Bing은 QPS(초당 쿼리 수) 또는 QPM(월별 쿼리 수) 할당량을 초과할 경우 항상 RateLimitExceeded를 반환합니다.<br/><br/>Bing은 QPS를 초과한 경우 HTTP 상태 코드 429, QPM을 초과한 경우 403을 반환합니다.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing이 호출자를 인증할 수 없는 경우 Bing은 InvalidAuthorization을 반환합니다. 예를 들어 `Ocp-Apim-Subscription-Key` 헤더가 없거나 구독 키가 잘못된 경우입니다.<br/><br/>둘 이상의 인증 방법을 지정한 경우 중복이 발생합니다.<br/><br/>오류가 InvalidAuthorization이면 HTTP 상태 코드는 401입니다.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|리소스에 액세스할 수 있는 사용 권한이 호출자에게 없는 경우 Bing은 InsufficientAuthorization을 반환합니다. 이 오류는 구독 키가 비활성화되었거나 만료된 경우에 발생할 수 있습니다. <br/><br/>오류가 InsufficientAuthorization이면 HTTP 상태 코드는 403입니다.

- 다음은 이전 오류 코드를 새 코드에 매핑합니다. v5 오류 코드에 대한 종속성이 있는 경우, 코드를 적절하게 업데이트합니다.

|버전 5 code|버전 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
사용 안 함|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
차단|InvalidRequest.Blocked

### <a name="query-parameters"></a>쿼리 매개 변수

- `modulesRequested` 쿼리 매개 변수의 이름을 [모듈](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested)로 변경했습니다.  

### <a name="object-changes"></a>개체 변경 내용

- [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos)의 `nextOffsetAddCount` 필드 이름을 `nextOffset`으로 변경했습니다. 오프셋을 사용하는 방식도 변경되었습니다. 앞에서는 [offset](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) 쿼리 매개 변수를 `nextOffset` 값에 이전 오프셋 값과 결과의 비디오 수를 더한 값으로 설정했습니다. 이번에는 간단하게 `offset` 쿼리 매개 변수를 `nextOffset` 값으로 설정했습니다.  
  
- `relatedVideos` 필드의 데이터 형식을 `Video[]`에서 [VideosModule](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule)로 변경했습니다([VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails) 참조).