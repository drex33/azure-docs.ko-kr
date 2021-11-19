---
title: Azure API Management 캐싱 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 캐싱 정책에 대해 알아봅니다. 예제를 살펴보고 사용 가능한 추가 리소스를 확인합니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: danlep
ms.openlocfilehash: 4b0ac3ef2e9b35f9003ca4d73e9d0fc58a112d2e
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132810105"
---
# <a name="api-management-caching-policies"></a>API Management 캐싱 정책

이 문서에서는 다음 API Management 정책에 참조할 내용을 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

> [!IMPORTANT]
> 기본 제공 캐시는 휘발성이며 동일한 API Management 서비스의 동일한 지역에 있는 모든 유닛에서 공유됩니다.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> 캐싱 정책

- 응답 캐싱 정책
    - [캐시에서 가져오기](#GetFromCache) - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.
    - [캐시에 저장](#StoreToCache) - 지정된 캐시 제어 구성에 따라 응답을 캐시합니다.
- 값 캐싱 정책
    - [캐시에서 값 가져오기](#GetFromCacheByKey) - 키로 캐시된 항목을 검색합니다.
    - [값을 캐시에 저장](#StoreToCacheByKey) - 키로 캐시에 항목을 저장합니다.
    - [캐시에서 값을 제거](#RemoveCacheByKey) - 키로 캐시에서 항목을 제거합니다.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> 캐시에서 가져오기
`cache-lookup` 정책을 사용하여 캐시 조회를 수행하며 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다. 이 정책은 응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다. 응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

> [!NOTE]
> 이 정책에는 해당하는 [캐시에 저장](#StoreToCache) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

> [!NOTE]
> `vary-by-query-parameter`를 사용할 때 rewrite-uri 템플릿에서 매개 변수를 선언하거나 `copy-unmatched-params` 특성을 `false`로 설정할 수 있습니다. 이 플래그를 사용하지 않도록 설정하면 선언되지 않은 매개 변수가 백 엔드로 전송됩니다.

### <a name="examples"></a>예

#### <a name="example"></a>예제

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>정책 식을 사용하는 예제
이 예제에서는 지원 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 API Management 응답 캐싱 기간을 구성하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky와 함께 하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)(영문)에서 25:25 지점으로 빨리 감기하면서 참조하세요.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|cache-lookup|루트 요소입니다.|예|
|vary-by-header|지정된 헤더 값당 시작 캐싱 응답입니다(예: Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match).|예|
|vary-by-query-parameter|지정된 쿼리 매개 변수의 값에 따라 응답 캐싱을 시작합니다. 단일 또는 여러 매개 변수를 입력합니다. 세미콜론을 구분 기호로 사용합니다. 지정하지 않은 경우 모든 쿼리 매개 변수가 사용됩니다.|예|

### <a name="attributes"></a>특성

| Name                           | Description                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | `true`로 설정하면 Authorization 헤더를 포함하는 요청의 캐싱을 허용합니다.                                                                                                                                                                                                                                                                        | 예       | false             |
| caching-type               | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external`[Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 예       | `prefer-external` |
| downstream-caching-type        | 이 특성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> -   none - 다운스트림 캐싱이 허용되지 않습니다.<br />-   private - 다운스트림 프라이빗 캐싱이 허용됩니다.<br />-   public - 프라이빗 및 공유 다운스트림 캐싱이 허용됩니다.                                                                                                          | No       | 없음              |
| must-revalidate                | 다운스트림 캐싱을 사용하는 경우 이 특성이 게이트웨이 응답에서 `must-revalidate` 캐시 제어 지시문을 설정 또는 해제합니다.                                                                                                                                                                                                                      | 예       | true              |
| vary-by-developer              | 요청에 포함된 [구독 키](./api-management-subscriptions.md)를 소유한 개발자 계정별로 응답을 캐시하려면 `true`로 설정합니다.                                                                                                                                                                                                                                                                                                  | 예      |         False          |
| vary-by-developer-groups       | [사용자 그룹](./api-management-howto-create-groups.md)별 캐시 응답을 위해서는 `true`로 설정합니다.                                                                                                                                                                                                                                                                                                             | 예      |       False            |

### <a name="usage"></a>사용량
이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** 모든 범위

## <a name="store-to-cache"></a><a name="StoreToCache"></a> 캐시에 저장
`cache-store` 정책은 지정된 캐시 설정에 따라 응답을 캐시합니다. 이 정책은 응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다. 응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

> [!NOTE]
> 이 정책에는 해당하는 [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-store duration="seconds" cache-response="true | false" />
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>정책 식을 사용하는 예제
이 예제에서는 지원 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 API Management 응답 캐싱 기간을 구성하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky와 함께 하는 추가 API Management 기능](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky)(영문)에서 25:25 지점으로 빨리 감기하면서 참조하세요.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|cache-store|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| Name             | Description                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | 캐시된 항목의 TTL(Time-to-Live)로 초 단위로 지정합니다.     | 예      | 해당 없음               |
| cache-response         | 현재 HTTP 응답을 캐시하려면 true로 설정합니다. 특성이 생략되거나 false로 설정된 경우 상태 코드가 `200 OK`인 HTTP 응답만 캐시됩니다.                           | 예      | false               |

### <a name="usage"></a>사용량
이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** outbound
- **정책 범위:** 모든 범위

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> 캐시에서 값 가져오기
`cache-lookup-value` 정책을 사용하여 키별 캐시 조회를 수행하며 캐시된 값을 반환합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

> [!NOTE]
> 이 정책에는 해당하는 [값을 캐시에 저장](#StoreToCacheByKey) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>예제
이 정책에 대한 자세한 내용과 예제는 [Azure API Management의 사용자 지정 캐싱](./api-management-sample-cache-by-key.md)을 참조하세요.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|cache-lookup-value|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| Name             | Description                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external`[Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 예       | `prefer-external` |
| default-value    | 캐시 키 조회 시 누락 항목이 있는 경우 변수에 할당할 값입니다. 이 특성을 지정하지 않으면 `null`이 할당됩니다.                                                                                                                                                                                                           | 예       | `null`            |
| key              | 조회에 사용할 캐시 키 값입니다.                                                                                                                                                                                                                                                                                                                       | 예      | 해당 없음               |
| variable-name    | 조회에 성공한 경우 조회된 값이 할당될 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)의 이름입니다. 조회 결과 누락이 발생하면 변수가 설정되지 않습니다.                                       | 예      | 해당 없음               |

### <a name="usage"></a>사용량
이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** 모든 범위

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> 값을 캐시에 저장
`cache-store-value`는 키로 캐시 스토리지을 수행합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

> [!NOTE]
> 이 정책에 따라 수행된, 캐시에 값을 저장하는 작업은 동기화됩니다. 저장된 값은 [캐시에서 값 가져오기](#GetFromCacheByKey) 정책을 사용하여 검색할 수 있습니다. 그러나 캐시에 값을 저장하는 비동기 작업이 여전히 진행 중일 수 있으므로 저장된 값이 즉시 검색되지 않을 수 있습니다. 

### <a name="policy-statement"></a>정책 문

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>예제
이 정책에 대한 자세한 내용과 예제는 [Azure API Management의 사용자 지정 캐싱](./api-management-sample-cache-by-key.md)을 참조하세요.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|cache-store-value|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| Name             | Description                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external`[Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 예       | `prefer-external` |
| duration         | 제공된 기간 값 동안 값(초 단위로 지정)이 캐시됩니다.                                                                                                                                                                                                                                                                                 | 예      | 해당 없음               |
| key              | 값을 저장할 캐시 키입니다.                                                                                                                                                                                                                                                                                                                   | 예      | 해당 없음               |
| 값            | 캐시될 값입니다.                                                                                                                                                                                                                                                                                                                                     | 예      | 해당 없음               |
### <a name="usage"></a>사용량
이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** 모든 범위

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> 캐시에서 값 제거
`cache-remove-value` 키로 식별된 캐시 항목을 삭제합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

#### <a name="policy-statement"></a>정책 문

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>예제

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>요소

|이름|Description|필수|
|----------|-----------------|--------------|
|cache-remove-value|루트 요소입니다.|예|

#### <a name="attributes"></a>특성

| Name             | Description                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external`[Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 예       | `prefer-external` |
| key              | 캐시에서 제거할 이전에 캐시된 값의 키입니다.                                                                                                                                                                                                                                                                                        | 예      | 해당 없음               |

#### <a name="usage"></a>사용량
이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](./api-management-policies.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](./policy-reference.md)
