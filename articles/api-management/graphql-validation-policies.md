---
title: GraphQL 요청에 대 한 Azure API Management 유효성 검사 정책 | Microsoft Docs
description: GraphQL 요청에 대 한 유효성을 검사 하 고 권한을 부여 하기 위해 Azure API Management에서 사용할 수 있는 새 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: 02225e0a07ef6567b8b190d9d31a957a69adbc4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102723"
---
# <a name="api-management-policy-to-validate-and-authorize-graphql-requests-preview"></a>GraphQL 요청 (미리 보기)의 유효성을 검사 하 고 권한을 부여 하는 API Management 정책

이 문서에서는 API Management으로 가져온 [GRAPHQL API](graphql-api.md) 에 대 한 요청의 유효성을 검사 하 고 권한을 부여 하는 새 API Management 정책에 대 한 참조를 제공 합니다.

정책 추가 및 구성에 대 한 자세한 내용은 [API Management의 정책](./api-management-policies.md)을 참조 하세요.

## <a name="validation-policy"></a>유효성 검사 정책

| 정책 | 설명 |
| ------ | ----------- |
| [GraphQL 요청 유효성 검사](#validate-graphql-request) | GraphQL API에 대 한 요청의 유효성을 검사 하 고 권한을 부여 합니다. |


## <a name="validate-graphql-request"></a>GraphQL 요청 유효성 검사

`validate-graphql-request`정책은 GraphQL 요청의 유효성을 검사 하 고 특정 쿼리 경로에 대 한 액세스 권한을 부여 합니다. 잘못 된 쿼리는 "요청 오류"입니다. 권한 부여는 유효한 요청에 대해서만 수행 됩니다. 

**권한**  
GraphQL 쿼리에서는 평면화 된 스키마를 사용 하기 때문에 다음을 수행 합니다.
* 출력 형식의 리프 노드에 사용 권한을 적용할 수 있습니다. 
    * 변형, 쿼리 또는 구독
    * 형식 선언의 개별 필드입니다. 
* 사용 권한을 적용할 수 없습니다. 
    * 입력 형식
    * 조각
    * Unions
    * 인터페이스
    * Schema 요소   

**권한 부여 요소**  
여러 권한 부여 요소를 사용할 수 있습니다. 가장 구체적인 경로는 쿼리의 각 리프 노드에 대 한 적절 한 권한 부여 규칙을 선택 하는 데 사용 됩니다. 
* 각 권한 부여는 다른 작업을 선택적으로 제공할 수 있습니다.
* `if` 관리자는 절을 사용 하 여 조건부 작업을 지정할 수 있습니다. 

**검사 시스템**   
Path =의 정책은 `/__*` [검사](https://graphql.org/learn/introspection/) 시스템입니다. 검사 요청 (, 등)을 거부 하는 데 사용할 수 있습니다. `__schema` `__type`   

### <a name="policy-statement"></a>정책 문

```xml
<validate-graphql-request error-variable-name="variable name" max-size="size in bytes" max-depth="query depth">
    <authorize-path="query path, for example: /Query/list Users or /__*" action="allow|remove|reject" />
        <if condition="policy expression" action="allow|remove|reject" />
</validate-graphql-request>
```

### <a name="example"></a>예제

다음 예제에서는 GraphQL 쿼리의 유효성을 검사 하 고 거부 합니다.
* 100 kb 보다 큰 요청 또는 쿼리 깊이가 4 보다 큰 요청 
* 검사 시스템 및 쿼리에 액세스할 수 `list Users` 있습니다. 

```xml
<validate-graphql-request error-variable-name="name" max-size="102400" max-depth="4"> 
    <authorize path="/" action="allow" /> 
    <authorize path="/__*" action="reject" /> 
    <authorize path="/Query/list Users" action="reject" /> 
</validate-graphql-request> 
```

### <a name="elements"></a>요소

| 이름         | 설명                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-graphql-request` | 루트 요소입니다.                                                                                                                               | 예      |
| `authorize` | 이러한 요소 중 하나 이상을 추가 하 여 요청 및 필드 수준 오류를 모두 포함 하는 필드 수준 권한 부여를 제공 합니다.   | Yes |
| `if` | 필드 수준 권한 부여에 대 한 작업에 조건부 변경을 적용 하려면 이러한 요소 중 하나 이상을 추가 합니다. | 아니요 |

### <a name="attributes"></a>특성

| Name                       | 설명                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `error-variable-name` | 유효성 검사 오류를 로그할 `context.Variables`의 변수 이름입니다.  |   아니요    | 해당 없음   |
| `max-size` | 요청 페이로드의 최대 크기 (바이트)입니다. 허용되는 최댓값: 102,400바이트(100KB). (이 제한을 늘려야 하는 경우 [고객 지원팀](https://azure.microsoft.com/support/options/)에 문의하세요.) | 예       | 해당 없음   |
| `max-depth` | 정수입니다. 최대 쿼리 깊이입니다. | 예 | 6 |
| `path` | 권한 부여 유효성 검사를 실행할 쿼리 경로입니다. | 예 | 해당 없음 |
| `action` | 일치 필드에 대해 수행할 [동작](#request-actions) 입니다. 일치 조건이 지정 된 경우 변경 될 수 있습니다. |  예     | 해당 없음   |
| `condition` | [정책 식이](api-management-policy-expressions.md) 과 일치 하는지 여부를 결정 하는 부울 값입니다. 첫 번째 일치 조건이 사용 됩니다. | 아니요 | 해당 없음 |

### <a name="request-actions"></a>요청 작업

사용 가능한 작업은 다음 표에 설명 되어 있습니다.

|작업 |설명  |
|---------|---------|
|`reject`     | 요청 오류가 발생 하 고 요청이 백 엔드에서 전송 되지 않습니다.     |
|`remove`     | 필드 오류가 발생 하 고 요청에서 필드가 제거 됩니다.         |
|`allow`     | 필드는 백 엔드에 전달 됩니다.        |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="error-handling"></a>오류 처리

GraphQL 스키마에 대 한 유효성 검사에 실패 하거나 요청의 크기나 깊이가 실패 하는 것은 요청 오류 이며 오류 블록 (데이터 블록 없음)으로 인해 요청이 실패 합니다. 

[`Context.LastError`](api-management-error-handling-policies.md#lasterror)속성과 마찬가지로 모든 GraphQL 유효성 검사 오류는 변수에 자동으로 전파 됩니다 `GraphQLErrors` . 오류를 별도로 전파 해야 하는 경우 오류 변수 이름을 지정할 수 있습니다. 오류는 `error` 변수와 `GraphQLErrors` 변수에 푸시됩니다. 

## <a name="next-steps"></a>다음 단계

정책 작업에 대한 자세한 내용은 다음을 참조하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [API 변환](transform-api.md)
-   정책 문 및 해당 설정에 대한 전체 목록에 대한 [정책 참조](./api-management-policies.md)
-   [정책 샘플](./policy-reference.md)
-   [오류 처리](./api-management-error-handling-policies.md)
