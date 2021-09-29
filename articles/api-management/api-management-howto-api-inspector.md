---
title: 자습서 - 요청 추적을 사용하여 Azure API Management에서 API 디버그
description: 이 자습서의 단계에 따라 추적을 사용하도록 설정하고 Azure API Management에서 요청 처리 단계를 검사합니다.
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: danlep
ms.openlocfilehash: 59d095b45b10ad6ed093107cc8d4589e0915161b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643311"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>자습서: 요청 추적을 사용하여 API 디버그

이 자습서에서는 API를 디버그하고 문제를 해결하는 데 도움이 되도록 Azure API Management에서 요청 처리를 검사(추적)하는 방법을 설명합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 예제 호출 추적
> * 요청 처리 단계 검토

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API 검사기":::

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 다음 자습서를 완료합니다. [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서를 완료합니다.

## <a name="verify-allow-tracing-setting"></a>추적 허용 설정 확인 

API에 사용되는 구독에 대한 **추적 허용** 설정을 사용하도록 설정해야 합니다. 기본 제공되는 모든 액세스 구독을 사용하는 경우 기본적으로 사용하도록 설정됩니다. 포털에서 확인하려면 API Management 인스턴스로 이동하여 **구독** 을 선택합니다.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="구독 추적 허용":::

## <a name="trace-a-call"></a>호출 추적

1. [Azure Portal](https://portal.azure.com)에 로그인하고 API Management 인스턴스로 이동합니다.
1. **API** 를 선택합니다.
1. API 목록에서 **Demo Conference API** 를 선택합니다.
1. **테스트** 탭을 선택합니다.
1. **GetSpeakers** 작업을 선택합니다.
1. HTTP 요청 헤더에 **Ocp-Apim-Trace: True** 및 **Ocp-Apim-Subscription-Key** 의 유효한 값이 포함되어 있는지 확인합니다. 그렇지 않은 경우 **+ 헤더 추가** 를 선택하여 헤더를 추가합니다.
1. **보내기** 를 선택하여 API 호출을 수행합니다.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="API 추적 구성":::

> [!TIP]
> **Ocp-Apim-Subscription-Key** 가 HTTP 요청에 자동으로 채워지지 않는 경우 포털에서 검색할 수 있습니다. **구독** 을 선택하고 구독에 대한 컨텍스트 메뉴( **...** )를 엽니다. **키 표시/숨기기** 를 선택합니다. 필요한 경우 키를 다시 생성할 수도 있습니다. 그런 다음, 헤더에 키를 추가합니다.

## <a name="review-trace-information"></a>추적 정보 검토

1. 호출이 완료되면 **HTTP 응답** 의 **추적** 탭으로 이동합니다.
1. 다음 링크 중 하나를 선택하여 자세한 추적 정보로 이동합니다. **인바운드**, **백 엔드**, **아웃바운드**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="응답 추적 검토":::

    * **인바운드** - 호출자로부터 받은 원래 요청 API Management 및 요청에 적용된 정책을 보여줍니다. 예를 들어 [자습서: API 변환 및 보호](transform-api.md)에 정책을 추가한 경우 여기에 표시됩니다.

    * **백 엔드** - API Management가 API 백 엔드로 보낸 요청과 수신된 응답을 보여줍니다.

    * **아웃바운드** - 호출자에게 다시 보내기 전에 응답에 적용되는 정책을 보여줍니다.

    > [!TIP]
    > 각 단계는 API Management에서 요청이 수신된 이후에 경과된 시간도 표시합니다.

1. **메시지** 탭에서 **ocp-apim-trace-location** 헤더는 Azure Blob 스토리지에 저장된 추적 데이터의 위치를 보여줍니다. 필요한 경우 이 위치로 이동하여 추적을 검색합니다.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Azure Storage의 추적 위치":::
## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 예제 호출 추적
> * 요청 처리 단계 검토

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [수정 버전 사용](api-management-get-started-revise-api.md)
