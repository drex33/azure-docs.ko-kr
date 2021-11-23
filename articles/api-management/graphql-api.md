---
title: Azure Portal | 사용하여 GraphQL API 가져오기 Microsoft Docs
titleSuffix: ''
description: API Management GraphQL을 지원하는 방법, GraphQL API 추가 및 GraphQL 제한에 대해 알아봅니다.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 07a75203cce5800ba7ab61cd77ab9d3ae3c27791
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935050"
---
# <a name="import-a-graphql-api-preview"></a>GraphQL API 가져오기(미리 보기)

GraphQL은 API에 대한 오픈 소스 업계 표준 쿼리 언어입니다. 리소스에 대한 작업을 중심으로 설계된 엔드포인트 기반(또는 REST 스타일) API와 달리 GraphQL API는 광범위한 사용 사례 집합을 지원하며 데이터 형식, 스키마 및 쿼리에 집중합니다.

API Management GraphQL API 게시와 함께 제공되는 보안, 인증 및 권한 부여 문제를 해결합니다. API Management 사용하여 GraphQL API를 노출하면 다음을 수행할 수 있습니다.
* Azure Portal 통해 API로 GraphQL 서비스를 추가합니다.  
* 기존 액세스 제어 정책과 [새 정책을](graphql-validation-policies.md) 모두 적용하여 GraphQL 관련 공격을 보호하고 보호함으로써 GraphQL API를 보호합니다. 
* 스키마를 탐색하고 Azure 및 개발자 포털에서 GraphQL API에 대한 테스트 쿼리를 실행합니다. 

[!INCLUDE [preview-callout-graphql.md](./includes/preview/preview-callout-graphql.md)]

이 문서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * GraphQL API 사용의 이점에 대해 자세히 알아보세요.
> * API Management 인스턴스에 GraphQL API를 추가합니다.
> * GraphQL API를 테스트합니다.
> * API Management GraphQL API의 제한에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 기존 API Management 인스턴스. [없다면 하나 생성합니다](get-started-create-service-instance.md).
- A GraphQL API. 

## <a name="add-a-graphql-api"></a>GraphQL API 추가

1. API Management 인스턴스로 이동합니다.
1. 측면 탐색 메뉴의 **API** 섹션 아래에서 **API** 를 선택합니다.
1. **새 API 정의** 아래에서 **GraphQL** 아이콘을 선택합니다.

    :::image type="content" source="media/graphql-api/import-graphql-api.png" alt-text="API 목록에서 GraphQL 아이콘 선택":::

1. 대화 상자에서 **전체** 를 선택하고 필요한 양식 필드를 작성합니다.

    :::image type="content" source="media/graphql-api/create-from-graphql-schema.png" alt-text="GraphQL을 만들기 위한 필드 시연":::

    | 필드 | 설명 |
    |----------------|-------|
    | 표시 이름 | GraphQL API가 표시되는 이름입니다. |
    | 이름 | GraphQL API의 원시 이름입니다. 표시 이름을 입력하면 자동으로 채워집니다. |
    | GraphQL API 엔드포인트 | GraphQL API 엔드포인트 이름이 있는 기준 URL입니다. <br /> 예: *`https://example.com/your-GraphQL-name`* . 일반적인 ["StarType" GraphQL 엔드포인트를](https://swapi-graphql.netlify.app/.netlify/functions/index) 데모로 사용할 수도 있습니다. |
    | 스키마 파일 업로드 | 스키마 파일을 찾아 업로드하려면 선택합니다. |
    | Description | API에 대한 설명을 추가합니다. |
    | URL 구성표 | HTTP, HTTPS 또는 모두를 선택합니다. 기본 선택: *둘 다.* |
    | API URL 접미사| URL 접미사를 추가하여 이 API Management 인스턴스에서 이 API를 식별합니다. 접미사는 이 API Management 인스턴스에서 고유해야 합니다. |
    | 기준 URL | API 기준 URL을 표시하는 미지정 필드 |
    | 태그 | GraphQL API를 새 태그 또는 기존 태그와 연결합니다. |
    | 제품 | GraphQL API를 제품과 연결하여 게시합니다. |
    | 게이트웨이 | GraphQL API를 기존 게이트웨이와 연결합니다. 기본 게이트웨이 선택: *관리되는* 입니다. |
    | API 버전 관리 | GraphQL API를 버전 제어하도록 선택합니다. |
 
1. **만들기** 를 클릭합니다.

## <a name="test-your-graphql-api"></a>GraphQL API 테스트

1. API Management 인스턴스로 이동합니다.
1. 측면 탐색 메뉴의 **API** 섹션 아래에서 **API** 를 선택합니다.
1. **모든 API** 아래에서 GraphQL API를 선택합니다.
1. **테스트** 탭을 선택하여 테스트 콘솔에 액세스합니다. 
1. **헤더 아래에서:**
    1. **이름** 드롭다운 메뉴에서 헤더를 선택합니다.
    1. 값 필드에 값을 **입력합니다.**
    1. + 헤더 추가를 선택하여 **헤더를** 더 추가합니다.
    1. **휴지통 아이콘** 을 사용하여 헤더를 삭제합니다.
1. GraphQL API에 제품을 추가한 경우 제품 범위 **적용에서 제품 범위를** 적용합니다.
1. **쿼리 편집기에서** 다음 중 하나를 수행합니다.
    1. 측면 메뉴의 목록에서 하나 이상의 필드 또는 하위 필드를 선택합니다. 선택한 필드와 하위 필드가 쿼리 편집기에서 나타납니다.
    1. 쿼리 편집기에서 입력을 시작하여 쿼리를 작성합니다.
    
        :::image type="content" source="media/graphql-api/test-graphql-query.png" alt-text="쿼리 편집기에서 필드 추가 시연":::

1. 쿼리 변수 아래에서 **변수를** 추가하여 동일한 쿼리 또는 변경 을 다시 사용하며 다른 값을 전달합니다.
1. **보내기** 를 클릭합니다.
1. **응답을** 봅니다.

    :::image type="content" source="media/graphql-api/graphql-query-response.png" alt-text="테스트 쿼리 응답 보기":::

1. 이전 단계를 반복하여 다른 페이로드를 테스트합니다.
1. 테스트가 완료되면 테스트 콘솔을 종료합니다.

## <a name="limitations"></a>제한 사항

* GraphQL 통과만 지원됩니다. 
* API Management 단일 GraphQL API는 단일 GraphQL 백 엔드 엔드포인트에만 해당합니다.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
