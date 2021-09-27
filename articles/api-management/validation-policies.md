---
title: Azure API Management 유효성 검사 정책 | Microsoft Docs
description: Azure API Management에서 요청 및 응답의 유효성을 검사하는 데 사용할 수 있는 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 08/20/2021
ms.author: danlep
ms.openlocfilehash: 57bb68352f04b356ec7a60f9e354993404c46abd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658952"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>요청 및 응답의 유효성을 검사하는 API Management 정책

이 문서에서는 다음 API Management 정책에 참조할 내용을 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

유효성 검사 정책을 사용하여 OpenAPI 스키마에 대한 API 요청 및 응답의 유효성을 검사하고 헤더 또는 페이로드 삽입과 같은 취약점으로부터 보호합니다. Web Application Firewall을 대체하는 것은 아니지만 유효성 검사 정책은 고정적이고 미리 정의된 규칙을 사용하는 보안 제품에서 다루지 않는 추가적인 위협 클래스에 대응할 수 있는 유연성을 제공합니다.

## <a name="validation-policies"></a>정책 유효성 검사

- [콘텐츠 유효성 검사](#validate-content) - API 스키마에 대한 요청 또는 응답 본문의 크기 또는 JSON 스키마의 유효성을 검사합니다. 
- [매개 변수 유효성 검사](#validate-parameters) - API 스키마에 대한 요청 헤더, 쿼리 또는 경로 매개 변수의 유효성을 검사합니다.
- [헤더 유효성 검사](#validate-headers) - API 스키마에 대한 응답 헤더의 유효성을 검사합니다.
- [상태 코드 유효성 검사](#validate-status-code) - API 스키마에 대한 응답에서 HTTP 상태 코드의 유효성을 검사합니다.

> [!NOTE]
> 유효성 검사 정책에서 사용할 수 있는 API 스키마의 최대 크기는 4MB입니다. 스키마가 이 제한을 초과하는 경우 유효성 검사 정책은 런타임에 오류를 반환합니다. 이 제한을 늘리려면 [고객 지원팀](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="actions"></a>동작

각 유효성 검사 정책에는 API 요청의 엔터티나 API 스키마에 대한 응답의 유효성을 검사할 때 API Management가 수행하는 작업을 지정하는 특성이 포함되어 있습니다. API 스키마에 표시되는 요소에 대한 작업을 지정할 수 있으며, 정책에 따라서는 API 스키마에 표시되지 않는 요소에 대해서도 작업을 지정할 수 있습니다. 정책의 자식 요소에 지정된 작업은 부모에 지정된 동작을 재정의합니다.

사용 가능한 작업은 다음과 같습니다.

| 작업         | Description          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | 유효성 검사 건너뛰기. |
| 방지 | 요청 또는 응답 처리를 차단하고, 자세한 [유효성 검사 오류](#validation-errors)를 로그하고, 오류를 반환합니다. 첫 번째 오류 집합이 검색되면 처리가 중단됩니다. 
| 검색(detect) | 요청 또는 응답 처리를 중단하지 않고 [유효성 검사 오류](#validation-errors)를 로그합니다. |

## <a name="logs"></a>로그

정책 실행 중의 유효성 검사 오류에 대한 세부 정보는 `context.Variables`의 변수에 로그되며 이 변수는 정책 루트 요소의 `errors-variable-name` 특성에 지정되어 있습니다. 작업 `prevent`에 구성된 경우 유효성 검사 오류가 발생하면 추가 요청 또는 응답 처리가 차단되고 `context.LastError` 속성에도 전파됩니다. 

오류를 조사하려면 [추적](api-management-advanced-policies.md#Trace) 정책을 사용하여 컨텍스트 변수에서 오류를 [Application Insights](api-management-howto-app-insights.md)로 로그합니다.

## <a name="performance-implications"></a>성능 의미

유효성 검사 정책을 추가하면 API 처리량에 영향을 줄 수 있습니다. 다음과 같은 일반적인 원칙이 적용됩니다.
* API 스키마 크기가 커질수록 처리량이 낮아집니다. 
* 요청이나 응답에서 페이로드가 커질수록 처리량이 낮아집니다. 
* API 스키마의 크기가 페이로드의 크기보다 성능에 더 큰 영향을 줍니다. 
* 수 메가바이트 크기의 API 스키마에 대해 유효성 검사를 수행하면 일부 조건에서 요청 또는 응답 시간 초과가 발생할 수 있습니다. 효과는 서비스의 **사용량** 및 **개발자** 계층에서 더 두드러지게 나타납니다. 

API 처리량에 대한 유효성 검사 정책의 영향을 평가하기 위해 예상되는 프로덕션 워크로드에서 부하 테스트를 수행하기를 권합니다.

## <a name="validate-content"></a>콘텐츠 유효성 검사

`validate-content` 정책은 API 스키마에 대한 요청 또는 응답 본문의 크기 또는 JSON 스키마의 유효성을 검사합니다. JSON 이외의 형식은 지원되지 않습니다.

### <a name="policy-statement"></a>정책 문

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>예제

다음 예제에서 요청 및 응답의 JSON 페이로드는 검색 모드에서 유효성 검사를 수행합니다. 페이로드가 100KB보다 큰 메시지는 차단됩니다. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-content | 루트 요소입니다.                                                                                                                               | 예      |
| 콘텐츠 | 이러한 요소 중 하나 이상을 추가하여 요청 또는 응답의 콘텐츠 형식 유효성을 검사하고, 지정된 작업을 수행합니다.  | 예 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-content-type-action | API 스키마에 지정되지 않은 콘텐츠 형식을 사용하여 요청 또는 응답에 대해 수행할 [작업](#actions)입니다. |  예     | 해당 없음   |
| max-size | 요청 또는 응답 본문의 최대 길이(바이트)로 `Content-Length` 헤더가 확인합니다. 요청 본문 또는 응답 본문이 압축된 경우 이 값은 압축을 푼 길이입니다. 허용되는 최댓값: 102,400바이트(100KB). (이 제한을 늘려야 하는 경우 [고객 지원팀](https://azure.microsoft.com/support/options/)에 문의하세요.) | 예       | 해당 없음   |
| size-exceeded-action | 요청이나 응답의 본문 크기가 `max-size`에서 지정한 크기를 초과했을 때 수행할 [작업](#actions)입니다. |  예     | 해당 없음   |
| errors-variable-name | 유효성 검사 오류를 로그할 `context.Variables`의 변수 이름입니다.  |   아니요    | 해당 없음   |
| type | 본문 유효성 검사를 실행할 콘텐츠 형식으로 `Content-Type` 헤더가 확인합니다. 값은 대/소문자를 구분하지 않습니다. 비어 있는 경우 API 스키마에 지정된 모든 콘텐츠 형식에 적용됩니다. |   예    |  해당 없음  |
| validate-as | 일치하는 콘텐츠 형식의 요청 또는 응답 본문의 유효성 검사에 사용할 유효성 검사 엔진입니다. 현재 지원되는 유일한 값은 “json”입니다.   |  예     |  해당 없음  |
| action | 본문이 지정된 콘텐츠 형식과 일치하지 않는 요청이나 응답에 수행할 [작업](#actions)입니다.  |  예      | 해당 없음   |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, on-error

-   **정책 범위:** 모든 범위

## <a name="validate-parameters"></a>매개 변수 유효성 검사

`validate-parameters` 정책은 API 스키마에 대한 요청의 헤더, 쿼리 또는 경로 매개 변수의 유효성을 검사합니다.

> [!IMPORTANT]
> `2021-01-01-preview`보다 이전 버전의 관리 API를 사용하여 API를 가져온 경우 `validate-parameters` 정책이 작동하지 않을 수 있습니다. [API를 다시 가져올](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) 때 관리 API 버전 `2021-01-01-preview` 또는 그 이상이 필요합니다.


### <a name="policy-statement"></a>정책 문

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>예제

이 예제에서 모든 쿼리 및 경로 매개 변수의 유효성은 방지 모드와 검색 모드의 헤더에서 확인됩니다. 유효성 검사는 다양한 헤더 매개 변수에 의해 재정의됩니다.

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-parameters | 루트 요소입니다. 요청의 모든 매개 변수에 대한 기본 유효성 검사 작업을 지정합니다.                                                                                                                              | 예      |
| headers | 요청에서 헤더 매개 변수에 대한 기본 유효성 검사 작업을 재정의하려면 이 요소를 추가합니다.   | 예 |
| Query | 요청에서 쿼리 매개 변수에 대한 기본 유효성 검사 작업을 재정의하려면 이 요소를 추가합니다.  | 예 |
| 경로 | 요청에서 URL 경로 매개 변수에 대한 기본 유효성 검사 작업을 재정의하려면 이 요소를 추가합니다.  | 예 |
| 매개 변수(parameter) | 명명된 매개 변수에 대한 요소를 하나 이상 추가하여 유효성 검사 작업의 상위 수준 구성을 재정의합니다. | 예 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-parameter-action | API 스키마에 지정된 요청 매개 변수에 수행할 [작업](#actions)입니다. <br/><br/> `headers`, `query`, 또는 `path` 요소에서 제공되는 경우, 그 값은 `specified-parameter-action`의 값을 재정의하며 그 위치는 `validate-parameters` 요소 안입니다.  |  예     | 해당 없음   |
| unspecified-parameter-action | API 스키마에 지정되지 않은 요청 매개 변수에 수행할 [작업](#actions)입니다. <br/><br/>`headers`이나 `query` 요소에서 제공되는 경우 그 값은 `unspecified-parameter-action`의 값을 재정의하며 그 위치는 `validate-parameters` 요소 안입니다. |  예     | 해당 없음   |
| errors-variable-name | 유효성 검사 오류를 로그할 `context.Variables`의 변수 이름입니다.  |   아니요    | 해당 없음   |
| name | 유효성 검사 작업을 재정의할 매개 변수의 이름입니다. 값은 대/소문자를 구분하지 않습니다.  | 예 | 해당 없음 |
| action | 이름이 일치하는 매개 변수에 수행할 [작업](#actions)입니다. 매개 변수가 API 스키마에 지정된 경우 이 값은 상위 수준 `specified-parameter-action` 구성을 재정의합니다. 매개 변수가 API 스키마에 지정되지 않은 경우 이 값은 상위 수준 `unspecified-parameter-action` 구성을 재정의합니다.| 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="validate-headers"></a>헤더 유효성 검사

`validate-headers` 정책은 API 스키마에 대한 응답 헤더의 유효성을 검사합니다.

> [!IMPORTANT]
> `2021-01-01-preview`보다 이전 버전의 관리 API를 사용하여 API를 가져온 경우 `validate-headers` 정책이 작동하지 않을 수 있습니다. API를 다시 가져올 때 관리 API 버전 `2021-01-01-preview` 또는 그 이상이 필요합니다.

### <a name="policy-statement"></a>정책 문

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>예제

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-headers | 루트 요소입니다. 응답의 모든 헤더에 대한 기본 유효성 검사 작업을 지정합니다.                                                                                                                              | 예      |
| header | 명명된 헤더에 하나 이상의 요소를 추가하여 응답의 헤더에 대한 기본 유효성 검사 작업을 재정의합니다. | 예 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-header-action | API 스키마에 지정된 응답 헤더에 수행할 [작업](#actions)입니다.  |  예     | 해당 없음   |
| unspecified-header-action | API 스키마에 지정되지 않은 응답 헤더에 수행할 [작업](#actions)입니다.  |  예     | 해당 없음   |
| errors-variable-name | 유효성 검사 오류를 로그할 `context.Variables`의 변수 이름입니다.  |   아니요    | 해당 없음   |
| name | 유효성 검사 작업을 재정의할 헤더의 이름입니다. 값은 대/소문자를 구분하지 않습니다. | 예 | 해당 없음 |
| action | 이름이 일치하는 헤더에 수행할 [작업](#actions)입니다. 헤더가 API 스키마에 지정된 경우 이 값은 `specified-header-action`의 값을 재정의하며 그 위치는 `validate-headers` 요소 안입니다. 그렇지 않으면 헤더 유효성 검사 요소에서 `unspecified-header-action` 값을 재정의합니다. | 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** 아웃바운드, 오류 발생 시

-   **정책 범위:** 모든 범위

## <a name="validate-status-code"></a>상태 코드 유효성 검사

`validate-status-code` 정책은 API 스키마에 대한 응답에서 HTTP 상태 코드의 유효성을 검사합니다. 이 정책은 스택 추적과 같은 백 엔드 오류의 누출을 방지하는 데 사용할 수 있습니다. 

### <a name="policy-statement"></a>정책 문

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>예제

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-status-code | 루트 요소입니다.                                                                                                | 예      |
| status-code | HTTP 상태 코드에 하나 이상의 요소를 추가하여 응답의 상태 코드에 대한 기본 유효성 검사 작업을 재정의합니다. | 예 |

### <a name="attributes"></a>특성

| Name                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-status-code-action | API 스키마에 지정되지 않은 응답의 HTTP 상태 코드에 수행할 [작업](#actions)입니다.  |  예     | 해당 없음   |
| errors-variable-name | 유효성 검사 오류를 로그할 `context.Variables`의 변수 이름입니다.  |   아니요    | 해당 없음   |
| code | 유효성 검사 작업을 재정의할 HTTP 상태 코드입니다. | 예 | 해당 없음 |
| action | API 스키마에 지정되지 않은 일치 상태 코드에 수행할 [작업](#actions)입니다. API 스키마에 상태 코드가 지정된 경우에는 이 재정의가 적용되지 않습니다. | 예 | 해당 없음 | 

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** 아웃바운드, 오류 발생 시

-   **정책 범위:** 모든 범위


## <a name="validation-errors"></a>유효성 검사 오류
다음 표에는 유효성 검사 정책의 발생 가능한 모든 오류가 나열되어 있습니다. 

* **세부 정보** -오류를 조사하는 데 사용할 수 있습니다. 공개적으로 공유되지 않습니다.
* **공용 응답** -클라이언트에 반환되는 오류입니다. 구현 세부 정보를 누출하지 않습니다.

유효성 검사 정책에서 `prevent` 작업을 지정하고 오류를 생성하는 경우, API 관리의 응답에는 HTTP 상태 코드 400(인바운드 섹션에서 정책 적용 시) 및 502(아웃바운드 섹션에서 정책 적용 시)가 포함됩니다.


| **이름**   | **형식**                                                        | **유효성 검사 규칙** | **세부 정보**                                                                                                                                       | **공용 응답**                                                                                                                       | **동작**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | 요청의 본문은 {size} 바이트 길이이며 구성된 제한 {maxSize} 바이트를 초과합니다.                                                       | 요청의 본문은 {size} 바이트 길이이며 {maxSize} 바이트 제한을 초과합니다.                                                          | 검색/방지 |
||ResponseBody                                                    | SizeLimit           | 응답의 본문은 {size} 바이트 길이이며 구성된 제한 {maxSize} 바이트를 초과합니다.                                                      | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody                                                     | Unspecified         | 지정되지 않은 콘텐츠 형식 {messageContentType}은 허용되지 않습니다.                                                                                     | 지정되지 않은 콘텐츠 형식 {messageContentType}은 허용되지 않습니다.                                                                             | 검색/방지 |
| {messageContentType}                 | ResponseBody                                                    | Unspecified         | 지정되지 않은 콘텐츠 형식 {messageContentType}은 허용되지 않습니다.                                                                                     | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| | ApiSchema                                                       |                     | API의 스키마가 없거나 확인되지 못했습니다.                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
|                                      | ApiSchema                                                       |                     | API의 스키마에서 정의를 지정하지 않습니다.                                                                                                        | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | API의 스키마에 콘텐츠 형식 {messageContentType}과 연결된 정의 {definitionName}이 없습니다.                        | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | 요청 본문이 콘텐츠 형식 {messageContentType}과 연결된 정의 {definitionName}을 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}                  | 요청 본문이 콘텐츠 형식 {messageContentType}과 연결된 정의 {definitionName}을 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}            | 검색/방지 |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | 응답 본문이 콘텐츠 형식 {messageContentType}과 연결된 정의 {definitionName}을 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}                                       | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
|                                      | RequestBody                                                     | ValidationException | 콘텐츠 형식 {messageContentType}에 대한 요청 본문의 유효성을 검사할 수 없습니다.<br/><br/>{exception details}                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
|                                      | ResponseBody                                                    | ValidationException | 콘텐츠 형식 {messageContentType}에 대한 응답 본문의 유효성을 검사할 수 없습니다.<br/><br/>{exception details}                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| **validate-parameters / validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | QueryParameter/PathParameter/RequestHeader                  | Unspecified         | 지정되지 않은 {path parameter / query parameter / header} {paramName}은 허용되지 않습니다.                                                               | 지정되지 않은 {path parameter / query parameter / header} {paramName}은 허용되지 않습니다.                                                       | 검색/방지 |
| {headerName}                         | ResponseHeader                                                  | Unspecified         | 지정되지 않은 헤더 {headerName}은 허용되지 않습니다.                                                                                                   | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
|                                      |ApiSchema                                                       |                     | API의 스키마가 없거나 확인할 수 없습니다.                                                                                            | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
|                                       | ApiSchema                                                       |                     | API 스키마가 정의를 지정하지 않습니다.                                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | API의 스키마에 {definitionName} 정의가 포함되어 있지 않습니다. 이는 {query parameter / path parameter / header} {paramName}과 연결되어 있습니다.  | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {paramName}                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | 요청은 {query parameter / path parameter / header}{paramName}에 여러 값을 포함할 수 없습니다.                                           | 요청은 {query parameter / path parameter / header}{paramName}에 여러 값을 포함할 수 없습니다.                                   | 검색/방지 |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 응답은 {headerName} 헤더에 여러 값을 포함할 수 없습니다.                                                                              | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {paramName}                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | {query parameter / path parameter / header} {paramName}의 값이 정의를 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}                                          | {query parameter / path parameter / header} {paramName}의 값이 정의를 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}                              | 검색/방지 |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | {headerName} 헤더의 값이 정의를 따르지 않습니다.<br/><br/>{valError.Message} Line: {valError.LineNumber}, Position: {valError.LinePosition}                                                                              | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {paramName}                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | 정의에 따라 {query parameter / path parameter / header} {paramName}의 값을 구문 분석할 수 없습니다. <br/><br/>{ex.Message}                                | 정의에 따라 {query parameter / path parameter / header} {paramName}의 값을 구문 분석할 수 없습니다. <br/><br/>{ex.Message}                      | 검색/방지 |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 정의에 따라 {headerName} 헤더의 값을 구문 분석할 수 없습니다.                                                                  | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {paramName}                          | QueryParameter/PathParameter/RequestHeader                  | ValidationError     | {Query parameter / Path parameter / Header} {paramName}의 유효성을 검사할 수 없습니다.<br/><br/>{exception details}                                                                      | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | {headerName} 헤더의 유효성을 검사할 수 없습니다.<br/><br/>{exception details}                                                                                                          | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | Unspecified         | 응답 상태 코드 {status-code}는 허용되지 않습니다.                                                                                                | 내부 오류로 인해 요청을 처리할 수 없습니다. API 소유자에게 문의하세요.                                                       | 검색/방지 |


다음 표에서는 가능한 메시지 값과 함께 유효성 검사 오류의 가능한 모든 이유 값을 나열합니다.

|  **이유**         |    **Message** |
|---|---|  
| 잘못된 요청       |     컨텍스트 변수에 대한 {Details}, 클라이언트에 대한 {Public response}|
| 응답이 허용되지 않습니다  | 컨텍스트 변수에 대한 {Details}, 클라이언트에 대한 {Public response} |






## <a name="next-steps"></a>다음 단계

정책 작업에 대한 자세한 내용은 다음을 참조하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [API 변환](transform-api.md)
-   정책 문 및 해당 설정에 대한 전체 목록에 대한 [정책 참조](./api-management-policies.md)
-   [정책 샘플](./policy-reference.md)
-   [오류 처리](./api-management-error-handling-policies.md)
