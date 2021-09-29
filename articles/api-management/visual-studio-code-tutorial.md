---
title: 자습서 - API 가져오기 및 관리 - Azure API Management 및 Visual Studio Code | Microsoft Docs
description: 이 자습서에서는 Visual Studio Code용 Azure API Management 확장을 사용하여 API를 가져오고, 테스트하고, 관리하는 방법을 알아봅니다.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: ecdb97ad62a9b75279546493bbbf185f92fd29f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625775"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>자습서: Visual Studio Code용 API Management 확장을 사용하여 API 가져오기 및 관리

이 자습서에서는 Visual Studio Code용 API Management 확장을 일반적인 API Management 작업에 사용하는 방법에 대해 설명합니다. 친숙한 Visual Studio Code 환경을 사용하여 API를 가져오고, 테스트하고, 관리합니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * API Management로 API 가져오기
> * API 편집
> * API Management 정책 적용
> * API 테스트


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API Management 확장의 API":::

추가 API Management 기능에 대한 소개는 [Azure Portal](import-and-publish.md)을 사용하는 API Management 자습서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
- [Azure API Management 용어](api-management-terminology.md) 이해
- [Visual Studio Code](https://code.visualstudio.com/) 및 최신 [Visual Studio Code용 Azure API Management 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)을 설치했는지 확인
- [API Management 인스턴스 만들기](vscode-create-service-instance.md)

## <a name="import-an-api"></a>API 가져오기

다음 예에서는 JSON 형식의 OpenAPI 사양을 API Management로 가져옵니다. Microsoft는 이 예제에 사용되는 백엔드 API를 제공하고, Azure에서 이 API를 `https://conferenceapi.azurewebsites.net?format=json`에 호스트합니다.

1. Visual Studio Code의 작업 막대에서 Azure 아이콘을 선택합니다.
1. 탐색기 창에서 사용자가 만든 API Management 인스턴스를 펼칩니다.
1. 마우스 오른쪽 단추로 **API** 를 클릭하고, **OpenAPI 링크에서 가져오기** 를 선택합니다. 
1. 메시지가 표시되면 다음 값을 입력합니다.
    1. JSON 형식의 콘텐츠에 대한 **OpenAPI 링크**. 이 예의 경우 *https://conferenceapi.azurewebsites.net?format=json* 입니다.
    이 URL은 API 예제를 구현하는 서비스입니다. API Management는 이 주소로 요청을 전달합니다.
    1. **API 이름**(예: *demo-conference-api*) - API Management 인스턴스에서 고유합니다. 이 이름에는 문자, 숫자 및 하이픈만 포함될 수 있습니다. 첫 번째 및 마지막 문자는 영숫자여야 합니다. 이 이름은 API를 호출하는 경로에 사용됩니다.

API를 성공적으로 가져오면 탐색기 창에 표시되고, 사용 가능한 API 작업이 **작업** 노드 아래에 표시됩니다.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="탐색기 창의 가져온 API":::

## <a name="edit-the-api"></a>API 편집

API는 Visual Studio Code에서 편집할 수 있습니다. 예를 들어 편집기 창에서 API에 대한 Resource Manager JSON 설명을 편집하여 API에 액세스하는 데 사용되는 **http** 프로토콜을 제거합니다. 그런 다음, **파일** > **저장** 을 차례로 선택합니다.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON 설명 편집":::

OpenAPI 형식을 편집하려면 탐색기 창에서 마우스 오른쪽 단추로 API 이름을 클릭하고, **OpenAPI 편집** 을 선택합니다. 변경한 다음, **파일** > **저장** 을 차례로 선택합니다.

## <a name="apply-policies-to-the-api"></a>API에 정책 적용 

API Management는 API에 대해 구성할 수 있는 [정책](api-management-policies.md)을 제공합니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 정책은 API Management 인스턴스의 모든 API에 적용되는 전역적인 정책이거나 해당 범위가 특정 API 또는 API 작업으로 지정될 수 있습니다.

이 섹션에서는 API 응답을 변환하는 몇 가지 일반적인 아웃바운드 정책을 API에 적용하는 방법을 보여 줍니다. 다음 예제의 정책은 응답 헤더를 변경하고 응답 본문에 표시되는 원래 백 엔드 URL을 숨깁니다.

1. 탐색기 창의 가져온 *demo-conference-api* 아래에서 **정책** 을 선택합니다. 편집기 창에서 정책 파일이 열립니다. 이 파일은 API의 모든 작업에 대한 정책을 구성합니다. 

1. 파일을 `<outbound>` 요소의 다음 내용으로 업데이트합니다.
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * 첫 번째 `set-header` 정책은 데모를 위한 사용자 지정 응답 헤더를 추가합니다.
    * 두 번째 `set-header` 정책은 **X-Powered-By** 헤더를 삭제합니다(있는 경우). 이 헤더는 API 백 엔드에 사용되는 애플리케이션 프레임워크를 표시할 수 있으며, 게시자가 이를 제거하는 경우가 많습니다.
    * `redirect-content-urls` 정책은 API Management 게이트웨이를 통해 동등한 링크를 가리키도록 응답 본문의 링크를 다시 작성(마스킹)합니다.
    
1. 파일을 저장합니다. 메시지가 표시되면 **업로드** 를 선택하여 파일을 클라우드에 업로드합니다.

## <a name="test-the-api"></a>API 테스트

### <a name="get-the-subscription-key"></a>구독 키 가져오기

가져온 API와 적용되는 정책을 테스트하려면 API Management 인스턴스에 대한 구독 키가 필요합니다.

1. 탐색기 창에서 마우스 오른쪽 단추로 API Management 인스턴스의 이름을 클릭합니다.
1. **구독 키 복사** 를 선택합니다.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="구독 키 복사":::

### <a name="test-an-api-operation"></a>API 작업 테스트

1. 탐색기 창의 가져온 *demo-conference-api* 아래에서 **작업** 노드를 펼칩니다.
1. *GetSpeakers* 와 같은 작업을 선택한 다음, 작업을 마우스 오른쪽 단추로 클릭하고 **작업 테스트** 를 선택합니다.
1. 편집기 창에서 **Ocp-Apim-Subscription-Key** 옆에 있는 `{{SubscriptionKey}}`를 복사한 구독 키로 바꿉니다.
1. **요청 보내기** 를 선택합니다. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Visual Studio Code에서 API 요청 보내기":::

요청이 성공하면 백 엔드에서 **200 OK** 및 일부 데이터를 사용하여 응답합니다.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API 테스트 작업":::

응답에서 다음 세부 정보를 확인합니다.
* **Custom** 헤더가 응답에 추가됩니다.
* **X-Powered-By** 헤더가 응답에 표시되지 않습니다.
* API 백 엔드에 대한 URL이 API Management 게이트웨이(이 경우 `https://apim-hello-world.azure-api.net/demo-conference-api`)로 리디렉션됩니다.

### <a name="trace-the-api-operation"></a>API 작업 추적

API 작업을 디버그하는 데 도움이 되는 자세한 추적 정보를 보려면 **Ocp-APIM-Trace-Location** 옆에 표시되는 링크를 선택합니다. 

해당 위치의 JSON 파일에는 인바운드, 백 엔드 및 아웃바운드 추적 정보가 포함되어 있으므로 요청이 수행된 후 문제가 발생하는 위치를 확인할 수 있습니다.

> [!TIP]
> API 작업을 테스트하는 경우 API Management 확장에서 선택적 [정책 디버깅](api-management-debug-policies.md)(개발자 서비스 계층에서 사용 가능)을 허용합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 마우스 오른쪽 단추를 클릭하고 **Portal에서 열기** 를 선택하고 [API Management 서비스 및 리소스 그룹을 삭제](get-started-create-service-instance.md#clean-up-resources)하여 API Management 인스턴스를 제거합니다.

또는 **API Management 삭제** 를 선택하여 API Management 인스턴스만 삭제할 수 있습니다(이 작업은 해당 리소스 그룹이 삭제되지 않음).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="VS Code에서 API Management 인스턴스 삭제":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 API를 가져오고 관리하는 데 사용할 수 있는 Visual Studio Code용 API Management 확장의 몇 가지 기능을 소개했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * API Management로 API 가져오기
> * API 편집
> * API Management 정책 적용
> * API 테스트

API Management 확장은 API에서 사용할 수 있는 추가 기능을 제공합니다. 예를 들어 [정책을 디버그](api-management-debug-policies.md)(개발자 서비스 계층에서 사용 가능)하거나 [명명된 값](api-management-howto-properties.md)을 만들고 관리합니다.
