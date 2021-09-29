---
title: 자습서 - API Management의 모의 API 응답 - Azure Portal | Microsoft Docs
description: 이 자습서에서는 백 엔드가 실제 응답을 보낼 수 없는 경우 모의 응답을 반환하도록 API Management를 사용하여 API에 대한 정책을 설정합니다.
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: afccd6da3374147c77a2caf7bcc9b352b078c11c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646495"
---
# <a name="tutorial-mock-api-responses"></a>자습서: 모의 API 응답

백엔드 API를 APIM(API Management) API로 가져오거나 수동으로 만들고 관리할 수 있습니다. 이 자습서의 단계는 APIM을 사용하여 빈 API를 만들고, 수동으로 관리하고, 모의 응답을 반환하도록 API에 대한 정책을 설정하는 방법을 보여줍니다. 이 메서드를 통해 개발자는 백 엔드가 실제 응답을 보낼 수 없는 경우에도 APIM 인스턴스의 구현 및 테스트를 진행할 수 있습니다. 

모의 응답을 만드는 기능은 다양한 시나리오에서 유용할 수 있습니다.

+ API 외관이 먼저 설계되고 나중에 백 엔드가 구현된 경우 또는 백 엔드가 함께 개발되는 경우
+ 백 엔드가 일시적으로 작동하지 않거나 크기를 조정할 수 없는 경우

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 테스트 API 만들기 
> * 테스트 API에 작업 추가
> * 모의 응답 사용
> * 모의 API 테스트


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="모의 API 응답":::

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ [Azure API Management의 정책 개념](api-management-howto-policies.md)을 이해합니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

## <a name="create-a-test-api"></a>테스트 API 만들기 

이 섹션의 단계에서는 백 엔드 없는 빈 API를 만드는 방법을 보여 줍니다. 


1. Azure Portal에 로그인하고 API Management 인스턴스로 이동합니다.
1. **API** >  **+ API 추가** > **빈 API** 를 선택합니다.
1. **빈 API 만들기** 창에서 **전체** 를 선택합니다.
1. **표시 이름** 에 *테스트 API* 를 입력합니다.
1. **제품** 에서 **무제한** 을 선택합니다.
1. **게이트웨이** 에서 **관리** 를 선택합니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="빈 API 만들기":::

## <a name="add-an-operation-to-the-test-api"></a>테스트 API에 작업 추가

API는 하나 이상의 작업을 공개합니다. 이 섹션에서는 앞에서 만든 빈 API에 작업을 추가합니다. 이 섹션의 단계를 완료한 후 작업을 호출하면 오류가 발생합니다. [모의 응답 사용](#enable-response-mocking) 섹션의 단계를 완료하면 오류가 발생하지 않습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. 이전 단계에서 만든 API를 선택합니다.
1. **+ 작업 추가** 를 선택합니다.
1. **프런트 엔드** 창에서 다음 값을 입력합니다.

     | 설정             | Value                             | Description                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **표시 이름**    | *테스트 호출*                       | [개발자 포털](api-management-howto-developer-portal.md)에 표시되는 이름입니다.                                                                                                                                       |
    | **URL**(HTTP 동사) | GET                               | 미리 정의된 HTTP 동사 중 하나를 선택합니다.                                                                                                                                         |
    | **URL**             | */테스트*                           | API에 대한 URL 경로입니다.                                                                                                                                                                       |
    | **설명**     |                                   |  선택 사항이며, 개발자 포털에서 이 API를 사용하는 개발자에게 문서를 제공하는 데 사용되는 작업 설명입니다.                                                    |
    
1. URL, 표시 이름 및 설명 필드 아래에 있는 **응답** 탭을 선택합니다. 이 탭의 설정으로 이동하여 응답 상태 코드, 콘텐츠 형식, 예제 및 스키마를 정의합니다.
1. **+ 응답 추가** 를 선택하고, 목록에서 **200 정상** 을 선택합니다.
1. 오른쪽의 **표시** 제목 아래에서 **+표시 추가** 를 선택합니다.
1. *애플리케이션/json* 을 검색 상자에 입력하고 **애플리케이션/json** 콘텐츠 형식을 선택합니다.
1. **샘플** 텍스트 상자에 `{ "sampleField" : "test" }`를 입력합니다.
1. **저장** 을 선택합니다.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="API 작업 추가" border="false":::

이 예제의 필수 사항은 아니지만, 다음을 비롯한 API 작업에 대한 추가 설정을 다른 탭에서 구성할 수 있습니다.


|탭      |Description  |
|---------|---------|
|**쿼리**     |  쿼리 매개 변수를 추가합니다. 이름과 설명을 입력할 수 있을 뿐 아니라, 쿼리 매개 변수에 할당되는 값을 입력할 수 있습니다. 값 중 하나를 기본값으로 표시할 수 있습니다(선택 사항).        |
|**요청**     |  요청 콘텐츠 형식, 예제 및 스키마를 정의합니다.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

테스트 API에 작업을 추가하려면 [az apim api operation create](/cli/azure/apim/api/operation#az_apim_api_operation_create) 명령을 실행합니다.

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

[az apim api operation list](/cli/azure/apim/api/operation#az_apim_api_operation_list) 명령을 실행하여 API에 대한 모든 작업을 확인합니다.

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

작업을 제거하려면 [az apim api operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) 명령을 사용합니다. 이전 명령에서 작업 ID를 가져옵니다.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

이 문서의 나머지 부분에서 사용할 수 있도록 이 작업을 유지합니다.

---

## <a name="enable-response-mocking"></a>모의 응답 사용

1. [테스트 API 만들기](#create-a-test-api)에서 만든 API를 선택합니다.
1. 추가한 테스트 작업을 선택합니다.
1. 오른쪽 창에서 **디자인** 탭이 선택되어 있는지 확인합니다.
1. **인바운드 처리** 창에서 **+ 정책 추가** 를 선택합니다.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="처리 정책 추가" border="false":::

1. 갤러리에서 **모의 응답** 을 선택합니다.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="모의 응답 정책 타일" border="false":::

1. **API Management 응답** 텍스트 상자에 **200 정상, 애플리케이션/json** 을 입력합니다. 이 섹션은 API가 이전 섹션에서 정의한 응답을 반환해야 함을 나타냅니다.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="모의 응답 설정":::

1. **저장** 을 선택합니다.

    > [!TIP]
    > API에 대한 **모의가 활성화됨** 텍스트가 있는 노란색 막대는 API Management에서 반환된 응답이 [모의 정책](api-management-advanced-policies.md#mock-response)에 의해 모의 처리되고 백 엔드에서 생성되지 않음을 나타냅니다.

## <a name="test-the-mocked-api"></a>모의 API 테스트

1. [테스트 API 만들기](#create-a-test-api)에서 만든 API를 선택합니다.
1. **테스트** 탭을 선택합니다.
1. **테스트 호출** API가 선택되었는지 확인합니다. **보내기** 를 선택하여 테스트 호출을 수행합니다.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="모의 API 테스트":::

1. **HTTP 응답** 은 자습서의 첫 번째 섹션에서 샘플로 제공된 JSON을 표시합니다.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="모의 HTTP 응답":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 테스트 API 만들기
> * 테스트 API에 작업 추가
> * 모의 응답 사용
> * 모의 API 테스트

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
