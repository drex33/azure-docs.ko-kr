---
title: 논리 앱 워크플로에서 Azure Functions 호출
description: Azure Function을 만들고 호출하여 Azure Logic Apps로 만든 워크플로에서 고유한 코드를 실행합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 06/14/2021
ms.custom: devx-track-js
ms.openlocfilehash: b04ce478f214358c6cd55a35ebfe05f0863e053e
ms.sourcegitcommit: 5a27d9ba530aee0e563a1b0159241078e8c7c1e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112422682"
---
# <a name="create-and-run-your-own-code-from-workflows-in-azure-logic-apps-by-using-azure-functions"></a>Azure Functions를 사용하여 Azure Logic Apps의 워크플로에서 고유한 코드 만들기 및 실행

논리 앱 워크플로에서 특정 작업을 수행하는 코드를 실행하려는 경우 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 함수를 만들 수 있습니다. 이 서비스를 사용하면 완전한 앱 또는 인프라를 빌드하지 않고도 코드를 실행 수 있으므로 Node.js, C# 및 F# 함수를 만드는 데 유용합니다. [Azure 함수 내에서 논리 앱 워크플로를 호출](#call-logic-app)할 수도 있습니다. Azure Functions는 클라우드에서 서버리스 컴퓨팅을 제공하며 예를 들어 다음과 같은 특정 작업을 수행하는 데 유용합니다.

* Node.js 또는 C#의 함수를 사용하여 논리 앱의 동작을 확장합니다.
* 논리 앱 워크플로에서 계산을 수행합니다.
* 논리 앱 워크플로에서 고급 서식 지정 또는 컴퓨팅 필드를 적용합니다.

Azure Functions를 만들지 않고 코드 조각을 실행하려면 [인라인 코드를 추가하고 실행](../logic-apps/logic-apps-add-run-inline-code.md)할 수 있는 방법을 알아보세요.

> [!NOTE]
> Azure Logic Apps는 배포 슬롯이 사용되도록 설정된 Azure Functions 사용을 지원하지 않습니다. 이 시나리오가 작동하는 경우도 있지만 이 동작은 예측할 수 없으며 워크플로에서 Azure 함수를 호출하려고 할 때 권한 부여 문제가 발생할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 함수 앱은 직접 만든 함수와 더불어 Azure Functions를 사용하여 만든 함수의 컨테이너입니다.

  함수 앱이 없는 경우 [함수 앱을 먼저 만듭니다](../azure-functions/functions-get-started.md). 그런 후 Azure Portal의 논리 앱과는 별도로 또는 워크플로 디자이너의 [논리 앱 내에서](#create-function-designer) 함수를 만들 수 있습니다.

* 논리 앱을 사용할 때는 기존 또는 새로운 함수 앱 및 함수에 동일한 요구 사항이 적용됩니다.

  * 함수 앱과 논리 앱에는 동일한 Azure 구독이 있어야 합니다.

  * 새 함수 앱은 .NET 또는 JavaScript를 런타임 스택으로 사용해야 합니다. 기존 함수 앱에 새 함수를 추가하는 경우 C# 또는 JavaScript를 선택할 수 있습니다.

  * 함수는 **HTTP 트리거** 템플릿을 사용합니다.

    HTTP 트리거 템플릿은 논리 앱의 `application/json` 형식을 갖는 콘텐츠를 수락할 수 있습니다. 논리 앱에 함수를 추가하면 Azure 구독 내에서 이 템플릿을 통해 만든 사용자 지정 함수가 워크플로 디자이너에 표시됩니다.

  * 이전에는 [Swagger 파일](https://swagger.io/)로 알려진 [OpenAPI 정의](../azure-functions/functions-openapi-definition.md)를 정의하지 않는 한, 함수에 사용자 지정 경로가 사용되지 않습니다.

  * 함수에 OpenAPI 정의가 있는 경우 워크플로 디자이너는 함수 매개 변수를 사용하기 위한 보다 풍부한 환경을 제공합니다. 논리 앱이 OpenAPI 정의가 포함된 함수를 찾아 액세스할 수 있도록 [먼저 다음 단계를 수행하여 함수 앱을 설정](#function-swagger)합니다.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 포함하여 함수를 추가하려는 논리 앱

  함수를 실행하는 작업을 추가하려면 먼저 논리 앱이 트리거로 시작되어야 합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI 설명이 있는 함수 찾기

워크플로 디자이너에서 함수 매개 변수를 사용할 때 보다 풍부한 환경을 누리려면 이전에 [Swagger 파일](https://swagger.io/)이라고 불렸던 [OpenAPI 정의를 함수에 생성](../azure-functions/functions-openapi-definition.md)하세요. 논리 앱이 Swagger 설명이 포함된 함수를 찾아 사용할 수 있도록 함수 앱을 설정하려면 다음 단계를 수행합니다.

1. 함수 앱이 현재 실행되고 있는지 확인합니다.

1. 다음 단계에 따라 함수 앱에서 모든 원본이 허용되도록 [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 설정합니다.

   1. **함수 앱** 목록에서 함수 앱을 선택합니다. 오른쪽 창에서 **플랫폼 기능** > **CORS** 를 선택합니다.

      ![함수 앱 > "플랫폼 기능" > "CORS" 선택](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. **CORS** 아래에 별표( **`*`** ) 와일드카드 문자를 추가하되, 목록의 다른 원본은 모두 제거하고 **저장** 을 선택합니다.

      !["CORS*를 와일드카드 문자 "*"로 설정](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>HTTP 요청 내에서 속성 값에 액세스

웹후크 함수는 HTTP 요청을 입력으로 수락하고 해당 요청을 다른 함수에 전달할 수 있습니다. 예를 들어, Logic Apps에는 [DateTime 값을 변환하는 함수](../logic-apps/workflow-definition-language-functions-reference.md)가 있지만 이 기본 샘플 JavaScript 함수는 함수에 전달되는 요청 개체 내의 속성에 액세스하고 해당 속성 값에 대해 작업을 수행하는 방법을 보여 줍니다. 개체 내의 속성에 액세스하기 위해 이 예제에서는 [점(.) 연산자](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)를 사용합니다.

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

이 함수 내에서 수행되는 작업은 다음과 같습니다.

1. 이 함수는 `data` 변수를 만들고 `request` 개체 내의 `body` 개체를 해당 변수에 할당합니다. 이 함수는 점(.) 연산자를 사용하여 `request` 개체 내의 `body` 개체를 참조합니다.

   ```javascript
   var data = request.body;
   ```

1. 이 함수는 이제 `data` 변수를 통해 `date` 속성에 액세스하고, `ToDateString()` 함수를 호출하여 해당 속성 값을 DateTime 형식에서 DateString 형식으로 변환할 수 있습니다. 또한 이 함수는 다음과 같이 함수의 응답에서 `body` 속성을 통해 결과를 반환합니다.

   ```javascript
   body: data.date.ToDateString();
   ```

Azure에서 함수를 만들었으므로 [논리 앱에 함수를 추가](#add-function-logic-app)하는 단계를 따르세요.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>논리 앱 내에서 함수 만들기

워크플로 디자이너에서 기본 제공 Azure Functions 작업을 사용하여 논리 앱의 워크플로에서 직접 함수를 만들 수 있지만 JavaScript로 작성된 함수에만 이 메서드를 사용할 수 있습니다. 다른 언어의 경우 Azure Portal에서 Azure Functions 환경을 통해 함수를 만들 수 있습니다. 자세한 내용은 [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-get-started.md)를 참조하세요.

그러나 Azure에서 함수를 만들려면 함수의 컨테이너인 함수 앱이 이미 있어야 합니다. 함수 앱이 없는 경우 함수 앱을 먼저 만듭니다. [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-get-started.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱을 엽니다.

1. 함수를 만들고 추가하려면 시나리오에 적용되는 단계를 따릅니다.

   * 논리 앱 워크플로의 마지막 단계에서 **새 단계** 를 선택합니다.

   * 논리 앱 워크플로의 기존 단계 사이에서 화살표 위로 마우스를 이동한 후 더하기(+) 기호를 선택하고 **작업 추가** 를 선택합니다.

1. 검색 상자에 `azure functions`를 입력합니다. 작업 목록에서 **Azure 함수 선택** 이라는 작업을 선택합니다. 예를 들면 다음과 같습니다.

   ![Azure Portal에서 함수를 찾습니다.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 작업 목록이 열리면 **새 함수 만들기** 작업을 선택합니다.

   ![함수 앱 선택](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 함수 정의 편집기에서 함수를 정의합니다.

   1. **함수 이름** 상자에 함수에 대한 이름을 입력합니다.

   1. **코드** 상자에 함수 실행이 완료된 후에 논리 앱에 반환하려는 응답 및 페이로드를 포함한 코드를 함수 템플릿에 추가합니다. 완료되면 **만들기** 를 선택합니다.

   다음은 그 예입니다.

   ![함수 정의](./media/logic-apps-azure-functions/add-code-function-definition.png)

   템플릿의 코드에서 *`context` 개체* 는 논리 앱이 이후 단계에서 **요청 본문** 필드를 통해 전송하는 메시지를 참조합니다. 함수 내에서 `context` 개체의 속성에 액세스하려면 다음 구문을 사용합니다.

   `context.body.<property-name>`

   예를 들어, `context` 개체 내의 `content` 속성을 참조하려면 다음 구문을 사용합니다.

   `context.body.content`

   또한 템플릿 코드에는 함수가 해당 값에 대해 작업을 수행할 수 있도록 `data` 매개 변수의 값을 저장하는 `input` 변수도 포함되어 있습니다. JavaScript 함수 내에서 `data` 변수는 `context.body`를 빠르게 수행하는 방법이기도 합니다.

   > [!NOTE]
   > 여기서 `body` 속성은 `context` 개체에 적용되며, 함수에도 전달될 수 있는 작업의 출력에서 가져온 **Body** 토큰과 다릅니다.

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체* 이거나, 논리 앱이 함수에 보내는 메시지입니다. **요청 본문** 필드를 클릭하면 이전 단계의 출력에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 나타납니다. 이 예제에서는 컨텍스트 페이로드에 이메일 트리거의 **From** 토큰 값이 있는 `content`라는 속성이 포함되어 있다고 지정합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서 이 예제에서 **Received Time** 토큰을 대신 사용하면 큰따옴표를 추가하여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 사용할 메서드, 요청 헤더, 쿼리 매개 변수 또는 인증 등의 기타 세부 정보를 지정하려면 **새 매개 변수 추가** 목록을 열고 원하는 옵션을 선택합니다. 인증의 경우 선택한 함수에 따라 옵션이 달라집니다. [함수에 인증 사용](#enable-authentication-functions)을 참조하세요.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>논리 앱에 기존 함수 추가

워크플로 디자이너에서 다른 작업처럼 함수를 추가하면 논리 앱에서 기존 함수를 호출할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱을 엽니다.

1. 함수를 추가하려는 단계에서 **새 단계** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `azure functions`을 입력합니다. 작업 목록에서 **Azure 함수 선택** 이라는 작업을 선택합니다. 예를 들면 다음과 같습니다.

   ![Azure에서 함수를 찾습니다.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 함수 목록이 표시되면 함수를 선택합니다.

   ![함수 앱 및 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 정의(Swagger 설명)가 있고 [논리 앱이 해당 함수를 찾아 액세스할 수 있도록 설정된](#function-swagger) 함수에 대해 **Swagger 작업** 을 선택할 수 있습니다.

   ![함수 앱, “Swagger 작업”, 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체* 이거나, 논리 앱이 함수에 보내는 메시지입니다. **요청 본문** 필드를 클릭하면 이전 단계의 출력에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 나타납니다. 이 예제에서는 컨텍스트 페이로드에 이메일 트리거의 **From** 토큰 값이 있는 `content`라는 속성이 포함되어 있다고 지정합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서 이 예제에서 **Received Time** 토큰을 대신 사용하면 큰따옴표를 추가하여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 사용할 메서드, 요청 헤더, 쿼리 매개 변수 또는 인증 등의 기타 세부 정보를 지정하려면 **새 매개 변수 추가** 목록을 열고 원하는 옵션을 선택합니다. 인증의 경우 선택한 함수에 따라 옵션이 달라집니다. [함수에 인증 사용](#enable-authentication-functions)을 참조하세요.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>함수에서 논리 앱 호출

함수 내에서 논리 앱을 트리거하려면 논리 앱이 호출 가능한 엔드포인트를 제공하는 트리거로 시작해야 합니다. 예를 들어, **HTTP**, **Request**, **Azure Queues** 또는 **Event Grid** 트리거로 논리 앱을 시작할 수 있습니다. 함수 내에서 트리거의 URL에 HTTP POST 요청을 전송하고 해당 논리 앱에서 처리할 페이로드를 포함합니다. 자세한 내용은 [논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>함수에 인증 사용

로그인하고 자격 증명 또는 비밀을 입력할 필요 없이 Azure AD(Active Directory)에서 보호하는 리소스에 대한 액세스를 손쉽게 인증하려면 논리 앱에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)(이전 명칭: MSI(관리되는 서비스 ID))를 사용하면 됩니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. [Azure AD 인증에 관리 ID를 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에 대해 자세히 알아보세요.

시스템 할당 ID 또는 수동으로 생성된 사용자 할당 ID를 사용하도록 논리 앱을 설정하면 논리 앱의 함수가 동일한 ID를 인증에도 사용할 수 있습니다. 논리 앱의 함수에 대한 인증 지원을 자세히 알아보려면 [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조하세요.

함수를 통해 관리 ID를 설정하고 사용하려면 다음 단계를 수행합니다.

1. 논리 앱에서 관리 ID를 사용하도록 설정하고 해당 ID의 대상 리소스 액세스 권한을 설정합니다. [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)을 참조하세요.

1. 다음 단계를 수행하여 함수와 함수 앱에서 인증을 사용하도록 설정합니다.

   * [함수에 익명 인증 설정](#set-authentication-function-app)
   * [함수 앱에 Azure AD 인증 설정](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>함수에 익명 인증 설정

함수에서 논리 앱의 관리 ID를 사용하려면 함수의 인증 수준을 익명으로 설정해야 합니다. 그렇지 않으면 논리 앱이 "BadRequest" 오류를 throw합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택합니다. 이러한 단계에서는 "FabrikamFunctionApp"을 예제 함수 앱으로 사용합니다.

1. 함수 앱 창에서 **플랫폼 기능** 을 선택합니다. **개발 도구** 아래에서 **고급 도구(Kudu)** 를 선택합니다.

   ![Kudu용 고급 도구 열기](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Kudu 웹 사이트의 제목 표시줄에 있는 **디버그 콘솔** 메뉴에서 **CMD** 를 선택합니다.

   ![디버그 콘솔 메뉴에서 "CMD" 옵션을 선택합니다.](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 다음 페이지가 표시되면 폴더 목록에서 **사이트** > **wwwroot** > *함수* 를 선택합니다. 이러한 단계에서는 "FabrikamAzureFunction"을 예제 함수로 사용합니다.

   !["사이트" > "wwwroot" > 함수 선택](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 편집할 `function.json` 파일을 엽니다.

   !["function.json" 파일 옆에 있는 편집을 클릭합니다.](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. `bindings` 개체에서 `authLevel` 속성이 있는지 확인합니다. 속성이 있으면 속성 값을 `anonymous`로 설정합니다. 그렇지 않으면 해당 속성을 추가하고 값을 설정합니다.

   !["authLevel" 속성을 추가하고 "anonymous"로 설정합니다.](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 완료되면 설정을 저장하고 다음 섹션을 계속 진행합니다.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>함수 앱에 Azure AD 인증 설정

이 작업을 시작하기 전에 나중에 사용할 수 있도록 다음 값을 찾아 둡니다.

* 논리 앱을 나타내는 시스템 할당 ID에 대해 생성된 개체 ID.

  * 이 개체 ID를 생성하려면 [논리 앱의 시스템 할당 ID를 사용하도록 설정](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)합니다.

  * 그렇지 않고 이 개체 ID를 찾으려면 디자이너에서 논리 앱을 엽니다. 논리 앱 메뉴의 **설정** 에서 **ID** > **시스템 할당** 을 선택합니다.

* Azure AD(Azure Active Directory)에서 테넌트의 디렉터리 ID

  테넌트의 디렉터리 ID를 가져오려면 PowerShell 명령 [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount)를 실행하면 됩니다. 또는 Azure Portal에서 다음 단계를 따릅니다.

  1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택합니다.

  1. Azure AD 테넌트를 찾아 선택합니다. 이러한 단계에서는 "Fabrikam"을 예제 테넌트로 사용합니다.

  1. 테넌트의 메뉴에 있는 **관리** 에서 **속성** 을 선택합니다.

  1. 예를 들어 테넌트의 디렉터리 ID를 복사하고 나중에 사용하기 위해 저장합니다.

     ![Azure AD 테넌트의 디렉터리 ID 찾기 및 복사](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 액세스하려는 대상 리소스의 리소스 ID

  * 이러한 리소스 ID를 찾으려면 [Azure AD를 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 검토합니다.

  > [!IMPORTANT]
  > 이 리소스 ID는 필수 후행 슬래시를 포함하여 Azure AD의 예상 값과 정확히 일치해야 합니다.

  이 리소스 ID는 [시스템 할당 ID를 사용하도록 함수 작업을 설정](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)할 때 **대상 그룹** 속성에서 나중에 사용하는 값과 동일한 값이기도 합니다.

이제 함수 앱에 Azure AD 인증을 설정할 준비가 되었습니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택합니다.

1. 함수 앱 창에서 **플랫폼 기능** 을 선택합니다. **네트워킹** 아래에서 **인증/권한 부여** 를 선택합니다.

   ![인증 및 권한 부여 설정 보기](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. **App Service 인증** 설정을 **켜기** 로 변경합니다. **요청이 인증되지 않은 경우에 수행할 동작** 목록에서 **Azure Active Directory를 사용하여 로그인** 을 선택합니다. **인증 공급자** 에서 **Azure Active Directory** 를 선택합니다.

   ![Azure AD를 사용한 인증 켜기](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. **Azure Active Directory 설정** 창에서 다음 단계를 따릅니다.

   1. **관리 모드** 를 **고급** 으로 설정합니다.

   1. **클라이언트 ID** 속성에 논리 앱의 시스템 할당 ID에 대한 개체 ID를 입력합니다.

   1. **발급자 URL** 속성에 `https://sts.windows.net/` URL을 입력하고 Azure AD 테넌트의 디렉터리 ID를 추가합니다.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. **허용되는 토큰 대상 그룹** 속성에 액세스하려는 대상 리소스의 리소스 ID를 입력합니다.

      이 리소스 ID는 [시스템 할당 ID를 사용하도록 함수 작업을 설정](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)할 때 **대상 그룹** 속성에서 나중에 사용하는 값과 동일한 값입니다.

   현재 버전은 다음 예제와 유사합니다.

   ![Azure Active Directory 인증 설정](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 완료되면 **확인** 을 선택합니다.

1. 디자이너로 돌아가서 [관리 ID를 사용하여 액세스를 인증하는 단계](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)를 따릅니다.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
