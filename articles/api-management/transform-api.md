---
title: 자습서 - Azure API Management에서 API 변환 및 보호 | Microsoft Docs
description: 이 자습서에서는 API Management에서 변환 및 제한(속도 제한) 정책을 사용하여 API를 보호하는 방법을 알아봅니다.
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: danlep
ms.openlocfilehash: be58732e3279b7bfaccee76a8d5bc9d09ea664ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656401"
---
# <a name="tutorial-transform-and-protect-your-api"></a>자습서: API 변환 및 보호

이 자습서에서는 프라이빗 백 엔드 정보를 노출하지 있도록 API를 변환하는 방법을 보여줍니다. 예를 들어 백 엔드에서 실행되는 기술 스택에 대한 정보를 숨기려고 할 수 있습니다. 또한 API HTTP 응답의 본문에 표시되는 원래 URL을 숨기고, 대신 APIM 게이트웨이로 리디렉션하려고 할 수도 있습니다.

이 자습서에서는 Azure API Management로 속도 제한을 구성하여 백엔드 API에 대한 보호를 얼마나 쉽게 추가할 수 있는지 보여줍니다. 예를 들어 개발자가 API를 남용하지 않도록 API 호출 속도를 제한하려는 경우가 있습니다. 자세한 내용은 [API Management 정책](api-management-policies.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> -   API를 변환하여 응답 헤더 제거
> -   API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기
> -   속도 제한 정책(제한)을 추가하여 API 보호
> -   변환 테스트

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="포털의 정책":::

## <a name="prerequisites"></a>필수 구성 요소

-   [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
-   [Azure API Management의 정책 개념](api-management-howto-policies.md)을 이해합니다.
-   다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
-   또한 [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>API를 변환하여 응답 헤더 제거

이 섹션에서는 사용자에게 표시하지 않으려는 HTTP 헤더를 숨기는 방법을 보여줍니다. 이 예제에서는 HTTP 응답에서 다음과 같은 헤더를 삭제하는 방법을 보여줍니다.

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>원래 응답 테스트

원래 응답을 확인하려면 다음을 수행합니다.

1. API Management 서비스 인스턴스에서 **API** 를 선택합니다.
1. API 목록에서 **Demo Conference API** 를 선택합니다.
1. 화면 위쪽에 있는 **테스트** 탭을 선택합니다.
1. **GetSpeakers** 작업을 선택하고 **보내기** 를 선택합니다.

원래 응답은 다음과 비슷합니다.

:::image type="content" source="media/transform-api/original-response.png" alt-text="원래 API 응답":::

보시는 것처럼 응답에는 **X-AspNet-Version** 및 **X-Powered-By** 헤더가 포함됩니다.

### <a name="set-the-transformation-policy"></a>변환 정책 설정

1. **Demo Conference API** > **디자인** > **모든 작업** 을 선택합니다.
4. **아웃바운드 처리** 섹션에서 코드 편집기( **</>** ) 아이콘을 선택합니다.

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="아웃바운드 정책으로 이동" border="false":::

1. **&lt;아웃 바운드&gt;** 요소 내에 커서를 놓고 오른쪽 위 모서리에 있는 **코드 조각 표시** 를 선택합니다.
1. 오른쪽 창에서 **변환 정책** 아래에 있는 **HTTP 헤더 설정** 을 두 번 선택합니다(두 개의 정책 조각 삽입).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="HTTP 헤더 정책 설정":::

1. 다음과 같이 **\<outbound>** 코드를 수정합니다.

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="HTTP 헤더 설정":::

1. **저장** 을 선택합니다.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기

이 섹션에서는 API HTTP 응답의 본문에 표시되는 원래 URL을 숨기고, 대신 APIM 게이트웨이로 리디렉션하는 방법을 보여줍니다.

### <a name="test-the-original-response"></a>원래 응답 테스트

원래 응답을 확인하려면 다음을 수행합니다.

1. **Demo Conference API** > **테스트** 를 선택합니다.
1. **GetSpeakers** 작업을 선택하고 **보내기** 를 선택합니다.

    보시는 것처럼 응답에는 원래 백 엔드 URL이 포함됩니다.

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="응답의 원래 URL":::


### <a name="set-the-transformation-policy"></a>변환 정책 설정

1.  **Demo Conference API** > **모든 작업** > **디자인** 을 선택합니다.
1.  **아웃바운드 처리** 섹션에서 코드 편집기( **</>** ) 아이콘을 선택합니다.
1.  **&lt;아웃 바운드&gt;** 요소 내에 커서를 놓고 오른쪽 위 모서리에 있는 **코드 조각 표시** 를 선택합니다.
1.  오른쪽 창의 **변환 정책** 아래에서 **콘텐츠의 URL 마스킹** 을 선택합니다. 
1.  **저장** 을 선택합니다.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>속도 제한 정책(제한)을 추가하여 API 보호

이 섹션에서는 속도 제한을 구성하여 백 엔드 API에 대한 보호를 추가하는 방법을 보여 줍니다. 예를 들어 개발자가 API를 남용하지 않도록 API 호출 속도를 제한하려는 경우가 있습니다. 이 예제에서 제한은 각 구독 ID에 대해 15초당 3회 호출로 설정됩니다. 개발자는 15초 후에 API 호출을 다시 시도할 수 있습니다.

1.  **Demo Conference API** > **모든 작업** > **디자인** 을 선택합니다.
1.  **인바운드 처리** 섹션에서 코드 편집기( **</>** ) 아이콘을 선택합니다.
1.  **&lt;인바운드&gt;** 요소 내에 커서를 놓고 오른쪽 위 모서리에 있는 **코드 조각 표시** 를 선택합니다.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="인바운드 정책 설정" border="false":::

1.  오른쪽 창의 **액세스 제한 정책** 에서 **+ 키당 호출 속도 제한** 을 선택합니다.
1.  **rate-limit-by-key** 코드( **\<inbound\>** 요소)를 다음 코드로 수정합니다.

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>변환 테스트

이 시점에서 코드 편집기에서 코드를 살펴보면 정책은 다음과 같습니다.

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

이 섹션의 나머지 부분에서는 이 문서에서 설정한 정책 변환을 테스트합니다.

### <a name="test-the-stripped-response-headers"></a>삭제된 응답 헤더 테스트

1. **Demo Conference API** > **테스트** 를 선택합니다.
1. **GetSpeakers** 작업을 선택하고 **보내기** 를 선택합니다.

    보시는 것처럼 헤더가 삭제되었습니다.

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="삭제된 응답 헤더":::

### <a name="test-the-replaced-url"></a>대체된 URL 테스트

1. **Demo Conference API** > **테스트** 를 선택합니다.
1. **GetSpeakers** 작업을 선택하고 **보내기** 를 선택합니다.

    보시는 것처럼 URL이 대체되었습니다.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="대체된 URL":::

### <a name="test-the-rate-limit-throttling"></a>속도 제한 테스트

1. **Demo Conference API** > **테스트** 를 선택합니다.
1. **GetSpeakers** 작업을 선택합니다. **보내기** 를 세 번 연속으로 누릅니다.

    요청을 3회 보내면 **429 요청이 너무 많음** 응답이 표시됩니다.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="너무 많은 요청":::

1. 15초 정도 기다렸다가 **보내기** 를 다시 선택합니다. 이번에는 **200 정상** 응답이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> -   API를 변환하여 응답 헤더 제거
> -   API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기
> -   속도 제한 정책(제한)을 추가하여 API 보호
> -   변환 테스트

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [API 모니터링](api-management-howto-use-azure-monitor.md)
