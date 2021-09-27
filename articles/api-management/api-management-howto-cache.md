---
title: Azure API Management에서 캐싱을 추가하여 성능 향상 | Microsoft Docs
description: 대기 시간, 대역폭 사용 및 API Management 서비스 호출에 대한 웹 서비스 부하를 개선하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: danlep
ms.openlocfilehash: d571c2648dbc605ea8b760ba9b9384957fa51b2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666745"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Azure API Management에서 캐싱을 추가하여 성능 향상

API Management의 API 및 작업은 응답 캐싱을 사용하여 구성할 수 있습니다. 응답 캐싱은 API 호출자의 대기 시간과 API 공급자의 백 엔드 부하를 크게 줄일 수 있습니다.

> [!IMPORTANT]
> 기본 제공 캐시는 휘발성이며 동일한 API Management 서비스의 동일한 지역에 있는 모든 유닛에서 공유됩니다.

캐싱에 대한 자세한 내용은 [API Management 캐싱 정책](api-management-caching-policies.md)과 [Azure API Management의 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.

![캐시 정책](media/api-management-howto-cache/cache-policies.png)

학습할 내용:

> [!div class="checklist"]
> * API에 대한 응답 캐싱 추가
> * 실행 중인 캐싱 확인

## <a name="availability"></a>가용성

> [!NOTE]
> 내부 캐시는 Azure API Management의 **소비** 계층에서 사용할 수 없습니다. 그 대신 [외부 Azure Cache for Redis를 사용](api-management-howto-cache-external.md)할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

+ [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ [API 가져오기 및 게시](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>캐싱 정책 추가

이 예제에 나와 있는 캐싱 정책을 사용하면 **GetSpeakers** 작업에 대한 첫 번째 요청이 백 엔드 서비스의 응답을 반환합니다. 이 응답은 지정된 헤더 및 쿼리 문자열 매개 변수를 통해 캐시 및 입력됩니다. 일치하는 매개 변수를 사용하는, 작업에 대한 후속 호출은 캐시 기간 간격이 만료될 때까지 캐시된 응답을 반환합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. APIM 인스턴스로 이동합니다.
3. **API** 탭을 선택합니다.
4. API 목록에서 **Demo Conference API** 를 선택합니다.
5. **GetSpeakers** 를 선택합니다.
6. 화면 맨 위에서 **디자인** 탭을 선택합니다.
7. **인바운드 처리** 섹션에서 **</>** 아이콘을 클릭합니다.

    ![코드 편집기](media/api-management-howto-cache/code-editor.png)

8. **inbound** 요소에 다음 정책을 추가합니다.

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. **outbound** 요소에 다음 정책을 추가합니다.

   ```
   <cache-store duration="20" />
   ```

    **기간** 은 캐싱된 응답의 만료 간격을 지정합니다. 이 예제에서는 간격이 **20** 초입니다.

> [!TIP]
> [Azure API Management에서 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에서 설명한 대로 외부 캐시를 사용하는 경우 캐싱 정책의 `caching-type` 특성을 지정할 수 있습니다. 자세한 내용은 [API Management 캐싱 정책](api-management-caching-policies.md)을 참조하세요.

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>작업 호출 및 캐싱 테스트
실행 중인 캐싱을 확인하려면 개발자 포털에서 작업을 호출합니다.

1. Azure Portal에서 APIM 인스턴스로 이동합니다.
2. **API** 탭을 선택합니다.
3. 캐싱 정책을 추가할 API를 선택합니다.
4. **GetSpeakers** 작업을 선택합니다.
5. 맨 위 오른쪽 메뉴에 **테스트** 탭을 클릭합니다.
6. **보내기** 를 누릅니다.

## <a name="next-steps"></a><a name="next-steps"> </a>다음 단계
* 캐싱 정책에 대한 자세한 내용은 [API Management 정책 참조][API Management policy reference]의 [캐싱 정책][Caching policies]을 참조하세요.
* 정책 식을 사용하여 키별 캐싱 항목에 대한 자세한 내용은 [Azure API Management에서 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.
* 외부 Azure Cache for Redis를 사용하는 방법에 대한 자세한 내용은 [Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)을 참조하세요.

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
