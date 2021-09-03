---
title: 셀프 서비스 등록 흐름에 사용자 지정 승인 추가 - Azure AD
description: 외부 ID 셀프 서비스 등록에서 사용자 지정 승인 워크플로에 API 커넥터 추가 -Azure AD(Azure Active Directory)
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
ms.openlocfilehash: c5d12c45554897a9acfef1d32a2216d1eb410133
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459581"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>셀프 서비스 등록에 사용자 지정 승인 워크플로 추가

[API 커넥터](api-connectors-overview.md)를 사용하여 테넌트에서 생성되는 게스트 사용자 계정을 관리할 수 있도록 셀프 서비스 등록을 통해 자체 사용자 지정 승인 워크플로와 통합할 수 있습니다.

이 문서에서는 승인 시스템과 통합하는 방법에 대한 예를 제공합니다. 이 예제에서는 셀프 서비스 등록 사용자 흐름에서 등록 프로세스 중에 사용자 데이터를 수집하고 승인 시스템에 전달합니다. 그러면 승인 시스템에서 다음을 수행할 수 있습니다.

- 사용자를 자동으로 승인하고 Azure AD에서 사용자 계정을 만들도록 허용합니다.
- 수동 검토를 트리거합니다. 요청이 승인되면 승인 시스템은 Microsoft Graph를 사용하여 사용자 계정을 프로비전합니다. 승인 시스템은 사용자에게 계정이 생성되었음을 알릴 수도 있습니다.

> [!IMPORTANT]
>
> - **2021년 7월 12일부터**, Azure AD B2B 고객이 사용자 지정 또는 기간 업무 애플리케이션에 대한 셀프 서비스 등록과 함께 사용하기 위해 새로운 Google 통합을 설정하는 경우 인증이 시스템 웹 보기로 이동될 때까지 Google ID를 사용한 인증이 작동하지 않습니다. [자세히 알아보기](google-federation.md#deprecation-of-web-view-sign-in-support).
> - **2021년 9월 30부터** Google은 [포함된 웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 [외부 사용자 초대](google-federation.md) 또는 [셀프 서비스 등록](identity-providers.md)을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다. [자세한 정보를 알아보세요](google-federation.md#deprecation-of-web-view-sign-in-support).

## <a name="register-an-application-for-your-approval-system"></a>승인 시스템용 애플리케이션 등록

승인 시스템이 Azure AD를 사용하여 인증하고 사용자를 만들 수 있는 권한을 갖도록 승인 시스템을 Azure AD 테넌트의 애플리케이션으로 등록해야 합니다. [Microsoft Graph에 대한 인증 및 권한 부여 기본 사항](/graph/auth/auth-concepts)에 대해 자세히 알아보세요.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
4. 애플리케이션 **이름**(예: _등록 승인_)을 입력합니다.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. **등록** 을 선택합니다. 다른 필드는 기본값 그대로 둘 수 있습니다.

   ![등록 단추를 강조 표시하는 스크린샷.](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 왼쪽 메뉴의 **관리** 에서 **API 권한** 을 선택하고 **권한 추가** 를 선택합니다.
7. **API 권한 요청** 페이지에서 **Microsoft Graph** 를 선택한 후 **애플리케이션 권한** 을 선택합니다.
8. **권한 선택** 에서 **사용자** 를 확장한 다음, **User.ReadWrite.All** 확인란을 선택합니다. 이 권한을 통해 승인 시스템은 승인 시 사용자를 만들 수 있습니다. 그런 다음 **권한 추가** 를 선택합니다.

   ![애플리케이션 페이지 등록](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. **API 권한** 페이지에서 **(테넌트 이름)에 대한 관리자 동의 허용** 을 선택한 후 **예** 를 선택합니다.
10. 왼쪽 메뉴의 **관리** 에서 **인증서 및 비밀** 을 선택한 다음, **새 클라이언트 암호** 를 선택합니다.
11. 비밀에 대한 **설명**(예: _승인 클라이언트 암호_)을 입력하고 클라이언트 암호가 **만료** 되는 기간을 선택합니다. 그런 다음, **추가** 를 선택합니다.
12. 클라이언트 암호의 값을 복사합니다.

    ![승인 시스템에서 사용할 클라이언트 암호 복사](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. **애플리케이션 ID** 를 클라이언트 ID로 사용하고 Azure AD를 인증하기 위해 생성한 **클라이언트 암호** 를 사용하도록 승인 시스템을 구성합니다.

## <a name="create-the-api-connectors"></a>API 커넥터 만들기

다음으로 셀프 서비스 등록 사용자 흐름에 대한 [API 커넥터를 만듭니다](self-service-sign-up-add-api-connector.md#create-an-api-connector). 승인 시스템 API에는 아래에 표시된 예제와 같이 두 개의 커넥터와 해당하는 엔드포인트가 필요합니다. 이러한 API 커넥터는 다음을 수행합니다.

- **승인 상태 확인**. 사용자가 ID 공급자를 통해 로그인한 후 즉시 승인 시스템에 호출을 보내 사용자에게 기존 승인 요청이 있는지 또는 이미 거부되었는지 확인합니다. 승인 시스템에서 자동 승인 결정만 수행하는 경우에는 이 API 커넥터가 필요하지 않을 수 있습니다. "승인 상태 확인" API 커넥터의 예입니다.

  ![승인 상태 API 커넥터 구성 확인](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **승인 요청** -사용자가 특성 컬렉션 페이지를 완료한 후, 사용자 계정을 만들기 전에 승인 시스템에 호출을 보내 승인을 요청합니다. 승인 요청은 자동으로 허용하거나 수동으로 검토할 수 있습니다. "승인 요청" API 커넥터의 예입니다. 

  ![승인 요청 API 커넥터 구성](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

이러한 커넥터를 만들려면 [API 커넥터 만들기](self-service-sign-up-add-api-connector.md#create-an-api-connector)의 단계를 따르세요.

## <a name="enable-the-api-connectors-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용하도록 설정

이제 다음 단계를 사용하여 셀프 서비스 등록 사용자 흐름에 API 커넥터를 추가합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **사용자 흐름** 을 선택하고 API 커넥터를 사용하도록 설정하려는 사용자 흐름을 선택합니다.
5. **API 커넥터** 를 선택하고 사용자 흐름의 다음 단계에서 호출하려는 API 엔드포인트를 선택합니다.

   - **등록 중 ID 공급자와 페더레이션한 후**: 승인 상태 API 커넥터(예: _승인 상태 확인_)를 선택합니다.
   - **사용자를 만들기 전에**: 승인 요청 API 커넥터, 예를 들어 _승인 요청_ 을 선택합니다.

   ![사용자 흐름에 API 추가](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. **저장** 을 선택합니다.

## <a name="control-the-sign-up-flow-with-api-responses"></a>API 응답을 사용하여 등록 흐름 제어

승인 시스템은 등록 흐름을 제어하기 위해 호출될 때 응답을 사용할 수 있습니다. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>"승인 상태 확인" API 커넥터에 대한 요청 및 응답

"승인 상태 확인" API 커넥터에서 API가 받은 요청의 예:

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google, Facebook, and Email One Time Passcode identity providers 
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

API로 전송되는 정확한 클레임은 ID 공급자가 제공하는 정보에 따라 달라집니다. '이메일'은 항상 전송됩니다.

#### <a name="continuation-response-for-check-approval-status"></a>"승인 상태 확인"에 대한 연속 응답

**승인 확인 상태** API 엔드포인트는 다음과 같은 경우에 연속 응답을 반환해야 합니다.

- 사용자가 이전에 승인을 요청하지 않았습니다.

연속 응답의 예:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>"승인 상태 확인"에 대한 차단 응답

**승인 확인 상태** API 엔드포인트는 다음과 같은 경우 차단 응답을 반환해야 합니다.

- 사용자 승인이 보류 중입니다.
- 사용자가 거부되었으며 승인을 다시 요청할 수 없습니다.

차단 응답의 예는 다음과 같습니다.

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>"승인 요청" API 커넥터에 대한 요청 및 응답

"승인 요청" API 커넥터에서 API가 받은 HTTP 요청의 예:

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

#### <a name="continuation-response-for-request-approval"></a>"승인 요청"에 대한 연속 응답

**승인 요청** API 엔드포인트는 다음과 같은 경우에 연속 응답을 반환해야 합니다.

- 사용자는 **_자동으로 승인_** 될 수 있습니다.

연속 응답의 예:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 연속 응답이 수신되면 Azure AD는 사용자 계정을 만들고 사용자를 애플리케이션으로 보냅니다.

#### <a name="blocking-response-for-request-approval"></a>"승인 요청"에 대한 차단 응답

**승인 요청** API 엔드포인트는 다음과 같은 경우 차단 응답을 반환해야 합니다.

- 사용자 승인 요청이 만들어졌으며 현재 보류 중입니다.
- 사용자 승인 요청이 자동으로 거부되었습니다.

차단 응답의 예는 다음과 같습니다.

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

응답의 `userMessage`가 사용자에게 표시됩니다. 예를 들어 다음과 같습니다.

![승인 보류 중 예제 페이지](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>수동 승인 후 사용자 계정 만들기

수동 승인을 얻은 후 사용자 지정 승인 시스템은 [Microsoft Graph](/graph/use-the-api)를 사용하여 [사용자](/graph/azuread-users-concept-overview) 계정을 만듭니다. 승인 시스템이 사용자 계정을 프로비전하는 방법은 사용자가 사용하는 ID 공급자에 따라 다릅니다.

### <a name="for-a-federated-google-or-facebook-user-and-email-one-time-passcode"></a>페더레이션된 Google 또는 Facebook 사용자와 일회용 암호를 이메일로 보내기

> [!IMPORTANT]
> 승인 시스템은 이 메서드를 사용하려면 `identities`, `identities[0]`, `identities[0].issuer`가 있고 `identities[0].issuer`가 'facebook', 'google' 또는 '메일'과 동일한지 명시적으로 확인해야 합니다.

사용자가 Google 또는 Facebook 계정으로 로그인하거나 일회성 암호를 이메일로 보내는 경우 [사용자 만들기 API](/graph/api/user-post-users?tabs=http)를 사용할 수 있습니다.

1. 승인 시스템은 사용자 흐름에서 HTTP 요청을 받습니다.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 승인 시스템은 Microsoft Graph를 사용하여 사용자 계정을 만듭니다.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| 매개 변수                                           | 필수 | 설명                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Yes      | API에 전송된 `email` 클레임을 사용하여 생성하고, `@` 문자를 `_`로 바꾸고 `#EXT@<tenant-name>.onmicrosoft.com`으로 미리 보류할 수 있습니다. |
| accountEnabled                                      | Yes      | `true`로 설정해야 합니다.                                                                                                                                                 |
| mail                                                | Yes      | API로 전송된 `email` 클레임에 해당합니다.                                                                                                               |
| userType                                            | 예      | `Guest`이어야 합니다. 이 사용자를 게스트 사용자로 지정합니다.                                                                                                                 |
| ID                                          | Yes      | 페더레이션된 ID 정보입니다.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | 아니요       | `displayName`, `city` 및 기타와 같은 기타 기본 제공 특성 매개 변수 이름은 API 커넥터에서 보낸 매개 변수와 같습니다.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | 아니요       | 사용자에 대한 사용자 지정 특성입니다. 매개 변수 이름은 API 커넥터에서 보낸 매개 변수와 같습니다.                                                            |

### <a name="for-a-federated-azure-active-directory-user-or-microsoft-account-user"></a>페더레이션된 Azure Active Directory 사용자 또는 Microsoft 계정 사용자

사용자가 페더레이션된 Azure Active Directory 계정 또는 Microsoft 계정을 사용하여 로그인하는 경우 [초대 API](/graph/api/invitation-post)를 사용하여 사용자를 만든 다음 필요에 따라 [사용자 업데이트 API](/graph/api/user-update)를 사용하여 사용자에게 더 많은 특성을 할당해야 합니다.

1. 승인 시스템은 사용자 흐름에서 HTTP 요청을 받습니다.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 승인 시스템은 API 커넥터에서 제공한 `email`을 사용하여 초대를 만듭니다.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress": "johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

응답의 예는 다음과 같습니다.

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 승인 시스템은 초대된 사용자의 ID를 사용하여 사용자 계정을 수집된 사용자 특성으로 업데이트합니다(선택 사항).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Function 빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 사용하여 시작하세요.
- [수동 승인 예제를 사용하여 게스트 사용자에 대한 셀프 서비스 등록](code-samples-self-service-sign-up.md#custom-approval-workflows)을 체크 아웃합니다.
