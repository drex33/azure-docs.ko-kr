---
title: 셀프 서비스 등록 흐름에 API 커넥터 추가 - Azure AD
description: 사용자 흐름에서 사용할 웹 API를 구성합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536665"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>사용자 흐름에 API 커넥터 추가

[API 커넥터](api-connectors-overview.md)를 사용하려면 먼저 API 커넥터를 만든 다음 사용자 흐름에서 사용하도록 설정합니다.

> [!IMPORTANT]
>
> - **2021년 7월 12일부터**, Azure AD B2B 고객이 사용자 지정 또는 기간 업무 애플리케이션에 대한 셀프 서비스 등록과 함께 사용하기 위해 새로운 Google 통합을 설정하는 경우 인증이 시스템 웹 보기로 이동될 때까지 Google ID를 사용한 인증이 작동하지 않습니다. [자세히 알아보기](google-federation.md#deprecation-of-web-view-sign-in-support).
> - **2021년 9월 30부터** Google은 [포함된 웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 [외부 사용자 초대](google-federation.md) 또는 [셀프 서비스 등록](identity-providers.md)을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다. [자세한 정보를 알아보세요](google-federation.md#deprecation-of-web-view-sign-in-support).

## <a name="create-an-api-connector"></a>API 커넥터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **모든 API 커넥터** 를 선택한 다음 **새 API 커넥터** 를 선택합니다.

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="만들기 환경에서 API 커넥터의 대상 URL 및 표시 이름과 같은 기본 구성을 제공합니다.":::

5. 호출의 표시 이름을 제공합니다. 예: **승인 상태 확인**
6. API 호출에 대한 **엔드포인트 URL** 을 제공합니다.
7. **인증 유형** 을 선택하고 API 호출에 대한 인증 정보를 구성합니다. [API 커넥터 보호](self-service-sign-up-secure-api-connector.md) 방법에 대해 알아봅니다.

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="만들기 환경에서 API 커넥터에 대한 인증 구성을 제공합니다.":::

8. **저장** 을 선택합니다.

## <a name="the-request-sent-to-your-api"></a>API로 전송된 요청
API 커넥터는 **HTTP POST** 요청으로 구체화하여 사용자 특성('클레임')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](/graph/api/resources/user#properties) 사용자 속성과 유사하게 직렬화됩니다. 

**요청 예**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**Azure Active Directory** > **External Identities** > **사용자 지정 사용자 특성** 환경에 나열된 사용자 속성과 사용자 지정 특성만 요청에 보낼 수 있습니다.

사용자 지정 특성은 디렉터리에 **extension_\<extensions-app-id>_AttributeName** 형식으로 존재합니다. API는 이와 동일한 직렬화된 형식으로 클레임을 수신해야 합니다. 사용자 지정 특성에 대한 자세한 내용은 [셀프 서비스 등록 흐름에 대한 사용자 지정 특성 정의](user-flow-add-custom-attributes.md)를 참조하세요.

또한 클레임은 일반적으로 모든 요청에서 전송됩니다.
- **UI 로캘('ui_locales')** - 디바이스에 구성된 최종 사용자의 로캘입니다. 이는 API에서 다국어 응답을 반환하는 데 사용할 수 있습니다.
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- **이메일 주소('email')** 또는 [**ID('ID')** ](/graph/api/resources/objectidentity) - 이러한 클레임은 API에서 애플리케이션에 인증하는 최종 사용자를 식별하는 데 사용할 수 있습니다.

> [!IMPORTANT]
> API 엔드포인트가 호출 될 때 클레임에 값이 없으면 클레임은 API로 전송되지 않습니다. API는 클레임이 요청에 없는 경우를 명시적으로 확인하고 처리하도록 설계해야 합니다.

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용하도록 설정

다음 단계를 수행하여 API 커넥터를 셀프 서비스 등록 사용자 흐름에 추가합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **사용자 흐름** 을 선택하고 API 커넥터를 추가하려는 사용자 흐름을 선택합니다.
5. **API 커넥터** 를 선택하고 사용자 흐름의 다음 단계에서 호출하려는 API 엔드포인트를 선택합니다.

   - **등록하는 동안 ID 공급자와 페더레이션 후**
   - **사용자를 만들기 전**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="'사용자를 만들기 전'과 같은 사용자 흐름의 단계에 사용할 API 커넥터를 선택합니다.":::

6. **저장** 을 선택합니다.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>등록하는 동안 ID 공급자와 페더레이션 후

API 커넥터는 로그인 프로세스의 해당 절차에서 ID 공급자(예: Google, Facebook, Azure AD)를 사용한 사용자 인증 후 즉시 호출됩니다. 이 단계는 사용자 특성을 수집하기 위해 사용자에게 표시되는 형식인 ***특성 모음 페이지*** 앞에 나옵니다.

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

API로 전송되는 정확한 클레임은 ID 공급자가 제공하는 정보에 따라 달라집니다. 'email'은 항상 전송됩니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 웹 API의 예상 응답 유형

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 다음 단계: 특성 컬렉션 페이지로 계속 진행되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환하는 경우 해당 클레임은 다음을 수행합니다.

- 특성 컬렉션 페이지의 입력 필드를 미리 채웁니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조하세요.

#### <a name="blocking-response"></a>차단 응답

차단 응답은 사용자 흐름을 종료합니다. 이 응답은 사용자에게 블록 페이지를 표시하여 사용자 흐름의 연속을 중지하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공하는 `userMessage`가 표시됩니다.

[차단 응답](#example-of-a-blocking-response)의 예를 참조하세요.

## <a name="before-creating-the-user"></a>사용자를 만들기 전

등록 프로세스의 이 단계에 API 커넥터가 포함된 경우 특성 모음 페이지 다음에 호출됩니다. 이 단계는 항상 Azure AD에서 사용자 계정을 만들기 전에 호출됩니다. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
API로 전송되는 정확한 클레임은 사용자로부터 수집된 정보나 ID 공급자가 제공하는 정보에 따라 달라집니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 웹 API의 예상 응답 유형

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답
- 유효성 검사 응답

#### <a name="continuation-response"></a>연속 응답
연속 응답은 사용자 흐름이 다음 단계: 디렉터리에 사용자 만들기로 계속 진행되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환하는 경우 해당 클레임은 다음을 수행합니다.

- 특성 컬렉션 페이지에서 클레임에 이미 할당된 모든 값을 재정의합니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조하세요.

#### <a name="blocking-response"></a>차단 응답
차단 응답은 사용자 흐름을 종료합니다. 이 응답은 사용자에게 블록 페이지를 표시하여 사용자 흐름의 연속을 중지하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공하는 `userMessage`가 표시됩니다.

[차단 응답](#example-of-a-blocking-response)의 예를 참조하세요.

### <a name="validation-error-response"></a>유효성 검사-오류 응답
 API가 유효성 검사 오류 응답으로 대응하는 경우 사용자 흐름은 특성 컬렉션 페이지에 유지되고 `userMessage`가 사용자에게 표시됩니다. 그런 다음, 사용자는 양식을 편집하고 다시 제출할 수 있습니다. 이 유형의 응답을 입력 유효성 검사에 사용할 수 있습니다.

[유효성 검사 오류 응답](#example-of-a-validation-error-response)의 예를 참조하세요.

## <a name="example-responses"></a>예제 응답

### <a name="example-of-a-continuation-response"></a>연속 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 매개 변수                                          | 형식              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                                            | String            | 예      | API의 버전입니다.                                                                                                                                                                                                                                                                |
| action                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 아니요       | 사용자 흐름에 대한 API 커넥터 구성 및 **사용자 특성** 에서 **수신할 클레임** 으로 선택한 경우 값을 디렉터리에 저장할 수 있습니다. **애플리케이션 클레임** 으로 선택한 경우에는 토큰에서 값을 반환할 수 있습니다.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 아니요       | 클레임은 `_<extensions-app-id>_`를 포함할 필요가 없으며 *선택 사항* 입니다. 반환된 값은 사용자로부터 수집된 값을 덮어쓸 수 있습니다.  |

### <a name="example-of-a-blocking-response"></a>차단 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| 매개 변수   | 형식   | 필수 | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 버전     | String | 예      | API의 버전입니다.                                                    |
| action      | String | 예      | 값은 `ShowBlockPage`여야 합니다.                                              |
| userMessage | String | 예      | 사용자에게 표시할 메시지입니다.                                            |

**차단 응답이 있는 최종 사용자 환경**

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="API가 차단 응답을 반환한 후 최종 사용자 환경이 어떻게 보이는지 보여 주는 예시 이미지.":::

### <a name="example-of-a-validation-error-response"></a>유효성 검사 오류 응답의 예

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| 매개 변수   | 형식    | 필수 | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 버전     | String  | 예      | API의 버전입니다.                                                    |
| action      | String  | 예      | 값은 `ValidationError`이어야 합니다.                                           |
| 상태      | 정수/문자열 | 예      | ValidationError 응답의 경우 값 `400` 또는 `"400"`이어야 합니다.  |
| userMessage | String  | 예      | 사용자에게 표시할 메시지입니다.                                            |

> [!NOTE]
> HTTP 상태 코드는 응답 본문의 "status" 값 외에 "400"이어야 합니다.

**유효성 검사 오류 응답이 포함된 최종 사용자 환경**

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="API가 유효성 검사 오류 응답을 반환한 후 최종 사용자 환경이 어떻게 보이는지 보여 주는 예시 이미지":::

## <a name="best-practices-and-how-to-troubleshoot"></a>모범 사례 및 문제 해결 방법

### <a name="using-serverless-cloud-functions"></a>서버리스 클라우드 함수 사용

[Azure Functions의 HTTP 트리거](../../azure-functions/functions-bindings-http-webhook-trigger.md)와 같은 서버리스 함수는 API 커넥터에서 사용할 API 엔드포인트를 만드는 방법을 제공합니다. [예를 들어](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts) 서버리스 클라우드 함수를 사용하여 유효성 검사 논리를 수행하고 등록을 특정 메일 도메인으로 제한할 수 있습니다. 서버리스 클라우드 함수는 보다 복잡한 시나리오에 대해 다른 웹 API, 데이터 저장소 및 기타 클라우드 서비스를 호출할 수도 있습니다.

### <a name="best-practices"></a>모범 사례
다음 사항을 확인합니다.
* API는 위에 설명된 대로 API 요청 및 응답 계약을 따릅니다. 
* API 커넥터의 **엔드포인트 URL** 은 올바른 API 엔드포인트를 가리킵니다.
* API는 의존하는 수신된 클레임의 null 값을 명시적으로 확인합니다.
* API는 [API 커넥터 보호](self-service-sign-up-secure-api-connector.md)에 설명된 인증 방법을 구현합니다.
* API는 유연하게 사용자 환경을 보장하기 위해 최대한 신속하게 응답합니다.
    * Azure AD는 응답을 받을 때까지 최대 *20초* 동안 기다립니다. 아무 것도 수신되지 않으면 API를 호출할 때 *한번 더 시도(재시도)* 합니다.
    * 서버리스 함수 또는 확장성 있는 웹 서비스를 사용하는 경우 프로덕션에서 API를 "활성" 또는 "웜" 상태로 유지하는 호스팅 계획을 사용합니다. Azure Functions의 경우 최소한 [프리미엄 플랜](../../azure-functions/functions-scale.md)을 사용하는 것이 좋습니다.
* API의 고가용성을 보장합니다.
* 다운스트림 API, 데이터베이스 또는 기타 API 종속성의 성능을 모니터링하고 최적화합니다.
* 엔드포인트는 Azure AD TLS 및 암호화 보안 요구 사항을 준수해야 합니다. 자세한 내용은 [TLS 및 암호화 도구 모음 요구 사항](../../active-directory-b2c/https-cipher-tls-requirements.md)을 참조하세요. 
 
### <a name="use-logging"></a>로깅 사용

일반적으로 [Application Insights](../../azure-functions/functions-monitoring.md)와 같은 웹 API 서비스에서 사용하도록 설정된 로깅 도구를 사용하여 API에서 예기치 않은 오류 코드, 예외 및 성능 저하 문제를 모니터링하는 것이 유용합니다.
* HTTP 200 또는 400이 아닌 HTTP 상태 코드를 모니터링합니다.
* 401 또는 403 HTTP 상태 코드는 일반적으로 인증에 문제가 있음을 나타냅니다. API 커넥터에서 API의 인증 계층 및 해당 구성을 한 번 더 확인합니다.
* 필요한 경우 개발에서 좀 더 적극적인 로깅 수준(예: "추적" 또는 "디버그")을 사용합니다.
* API에서 긴 응답 시간이 나타나는지 모니터링합니다. 

## <a name="next-steps"></a>다음 단계
- [셀프 서비스 등록에 사용자 지정 승인 워크플로를 추가](self-service-sign-up-add-approvals.md)하는 방법을 알아봅니다.
- [빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 시작해 보세요.
