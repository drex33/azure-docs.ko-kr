---
title: Azure Portal을 사용하여 수동으로 API 추가 | Microsoft Docs
description: 이 자습서에서는 APIM(API Management)을 사용하여 API를 수동으로 추가하는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: tutorial
ms.date: 04/26/2021
ms.author: danlep
ms.custom: fasttrack-edit
ms.openlocfilehash: 323c7543d87a9a82c55899269cd4dfca8a3aadb6
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487666"
---
# <a name="add-an-api-manually"></a>API를 수동으로 추가

이 문서의 단계에서는 Azure Portal을 사용하여 APIM(API Management) 인스턴스에 수동으로 API를 추가하는 방법을 보여 줍니다. 빈 API를 만들고 수동으로 정의하려는 일반적인 시나리오는 모의 API를 만들려는 경우입니다. 모의 API 만들기에 대한 자세한 내용은 [모의 API 응답 만들기](mock-api-responses.md)를 참조하세요.

기존 API를 가져오려는 경우 [관련 항목](#related-topics) 섹션을 참조하세요.

이 문서에서는 빈 API를 만들고 [httpbin.org](https://httpbin.org)(공용 테스트 서비스)를 백 엔드 API로 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API 만들기

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.
2. 왼쪽 메뉴에서 **+API 추가** 를 선택합니다.
3. 목록에서 **빈 API** 를 선택합니다.  
    ![빈 API](media/add-api-manually/blank-api.png)  
4. API에 대한 설정을 입력합니다. 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.
5. **만들기** 를 선택합니다.

이때 백 엔드 API의 작업에 매핑되는 API Management의 작업은 없습니다. API Management를 통해서가 아니라 백 엔드를 통해 노출되는 작업을 호출하는 경우 **404** 가 표시됩니다.

>[!NOTE] 
> 기본적으로 API를 추가할 때 일부 백 엔드 서비스에 연결되더라도 APIM은 사용자가 허용할 때까지 어떤 작업도 공개하지 않습니다. 백 엔드 서비스의 작업을 허용하려면 백 엔드 작업에 매핑되는 APIM 작업을 만듭니다.

## <a name="add-and-test-an-operation"></a>작업 추가 및 테스트

이 섹션에서는 백 엔드 "http://httpbin.org/get" 작업에 매핑하기 위해 "/get" 작업을 추가하는 방법을 보여줍니다.

### <a name="add-an-operation"></a>작업 추가

1. 이전 단계에서 만든 API를 선택합니다.
2. **+작업 추가** 를 클릭합니다.
3. **URL** 에서 **GET** 을 선택하고 리소스에 `/get`을 입력합니다.
4. **표시 이름** 으로 "*FetchData*"를 입력합니다.
5. **저장** 을 선택합니다.

### <a name="test-an-operation"></a>작업 테스트

Azure Portal에서 작업을 테스트합니다. 또는 **개발자 포털** 에서 테스트할 수도 있습니다.

1. **테스트** 탭을 선택합니다.
2. **FetchData** 를 선택합니다.
3. **보내기** 를 누릅니다.

"http://httpbin.org/get" 작업이 생성하는 응답이 표시됩니다. 작업을 변환하려면 [API 변환 및 보호](transform-api.md)를 참조하세요.

## <a name="add-and-test-a-parameterized-operation"></a>매개 변수가 있는 작업 추가 및 테스트

이 섹션에서는 매개 변수를 사용하는 작업을 추가하는 방법을 보여 줍니다. 이 예에서는 작업을 "http://httpbin.org/status/200"에 매핑합니다.

### <a name="add-the-operation"></a>작업 추가

1. 이전 단계에서 만든 API를 선택합니다.
2. **+작업 추가** 를 클릭합니다.
3. **URL** 에서 **GET** 을 선택하고 리소스에 `/status/{code}`를 입력합니다. 필요에 따라 이 매개 변수와 관련된 몇 가지 정보를 제공할 수 있습니다. 예를 들어 **형식** 에 대해 "*Number*"를 입력하고 **값** 에 대해 “*200*”(기본값)을 입력합니다.
4. **표시 이름** 으로 "GetStatus"를 입력합니다.
5. **저장** 을 선택합니다.

### <a name="test-the-operation"></a>작업 테스트 

Azure Portal에서 작업을 테스트합니다.  또는 **개발자 포털** 에서 테스트할 수도 있습니다.

1. **테스트** 탭을 선택합니다.
2. **GetStatus** 를 선택합니다. 기본적으로 코드 값은 "*200*"으로 설정됩니다. 이 값을 변경하여 다른 값을 테스트할 수 있습니다. 예를 들어 "*418*"을 입력합니다.
3. **보내기** 를 누릅니다.

    "http://httpbin.org/status/200" 작업이 생성하는 응답이 표시됩니다. 작업을 변환하려면 [API 변환 및 보호](transform-api.md)를 참조하세요.

## <a name="add-and-test-a-wildcard-operation"></a>와일드카드 작업 추가 및 테스트

이 섹션에서는 와일드카드 작업을 추가하는 방법을 보여줍니다. 와일드카드 작업을 사용하면 API 요청으로 임의의 값을 전달할 수 있습니다. 이전 섹션에 표시된 대로 별도의 GET 작업을 만드는 대신 와일드카드 GET 작업을 만들 수 있습니다.

### <a name="add-the-operation"></a>작업 추가

1. 이전 단계에서 만든 API를 선택합니다.
2. **+작업 추가** 를 클릭합니다.
3. **URL** 에서 **GET** 을 선택하고 리소스에 `/*`를 입력합니다.
4. **표시 이름** 에 "*WildcardGet*"을 입력합니다.
5. **저장** 을 선택합니다.

### <a name="test-the-operation"></a>작업 테스트 

Azure Portal에서 작업을 테스트합니다.  또는 **개발자 포털** 에서 테스트할 수도 있습니다.

1. **테스트** 탭을 선택합니다.
2. **WildcardGet** 을 선택합니다. 이전 섹션에서 테스트한 GET 작업 중 하나 이상을 시도하거나 지원되는 다른 GET 작업을 시도하세요. 

    예를 들어 **템플릿 매개 변수** 에서 와일드카드(*) 이름 옆의 값을 `headers`로 업데이트합니다. 이 작업은 들어오는 요청의 HTTP 헤더를 반환합니다.
1. **보내기** 를 누릅니다.

    "http://httpbin.org/headers" 작업이 생성하는 응답이 표시됩니다. 작업을 변환하려면 [API 변환 및 보호](transform-api.md)를 참조하세요.
  
>[!NOTE] 
> 통합하려는 백 엔드 API의 호스트가 작업 URL에서 후행 슬래시를 처리하는 방법을 이해하는 것이 중요할 수 있습니다. 자세한 내용은 이 [API Management FAQ](./api-management-faq.yml#how-does-api-management-handle-trailing-slashes-when-calling-backend-services-)를 참조하세요.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
