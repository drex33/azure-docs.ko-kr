---
title: 보호된 웹 API 앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 보호된 웹 API와 앱을 등록하는 데 필요한 정보를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a0b6e11720f6384b002f563dafddcd9d98aa549a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459485"
---
# <a name="protected-web-api-app-registration"></a>보호된 웹 API: 앱 등록

이 문서에서는 보호된 웹 API에 대한 앱 등록의 세부 사항을 설명합니다.

앱을 등록하는 일반적인 단계는 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](quickstart-register-app.md)을 참조하세요.

## <a name="accepted-token-version"></a>허용되는 토큰 버전

Microsoft ID 플랫폼은 v1.0 토큰과 v2.0 토큰을 발급할 수 있습니다. 이러한 토큰에 대한 자세한 내용은 [액세스 토큰](access-tokens.md)을 참조하세요.

API가 수락할 수 있는 토큰 버전은 Azure Portal에서 웹 API 애플리케이션 등록을 만들 때 선택한 **지원되는 계정 유형** 에 따라 달라질 수 있습니다.

- **지원되는 계정 유형** 값이 **조직 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)** 일 경우 허용되는 토큰 버전은 v2.0이어야 합니다.
- 그렇지 않은 경우 허용되는 토큰 버전은 v1.0이 될 수 있습니다.

애플리케이션을 만든 후 다음 단계를 수행하여 허용되는 토큰 버전을 확인하거나 변경할 수 있습니다.

1. Azure Portal에서 앱을 선택한 다음, **매니페스트** 를 선택합니다.
1. 매니페스트에서 **accessTokenAcceptedVersion** 속성을 찾습니다.
1. 이 값은 웹 API에서 허용하는 토큰 버전을 Azure AD(Azure Active Directory)에 지정합니다.
   - 이 값이 2이면 웹 API는 v2.0 토큰을 허용합니다.
   - 이 값이 **null** 이면 웹 API는 v1.0 토큰을 허용합니다.
1. 토큰 버전을 변경한 경우 **저장** 을 선택합니다.

웹 API는 허용하는 토큰 버전을 지정합니다. 클라이언트는 Microsoft ID 플랫폼에 웹 API에 대한 토큰을 요청할 때 웹 API가 허용하는 토큰 버전을 나타내는 토큰을 가져옵니다.

## <a name="no-redirect-uri"></a>리디렉션 URI 없음

웹 API는 대화형으로 로그인된 사용자가 없기 때문에 리디렉션 URI를 등록할 필요가 없습니다.

## <a name="exposed-api"></a>노출된 API

웹 API와 관련된 다른 설정은 노출된 API 및 노출된 범위 또는 앱 역할입니다.

### <a name="application-id-uri-and-scopes"></a>애플리케이션 ID URI 및 범위

범위는 일반적으로 `resourceURI/scopeName` 형식입니다. Microsoft Graph의 경우 범위에 바로 가기가 있습니다. 예를 들어 `User.Read`는 `https://graph.microsoft.com/user.read`의 바로 가기입니다.

앱 등록 중에 다음 매개 변수를 정의합니다.

- 리소스 URI
- 하나 이상의 범위
- 하나 이상의 앱 역할

기본적으로 애플리케이션 등록 포털에서는 리소스 URI `api://{clientId}`를 사용할 것을 권장합니다. 이 URI는 고유하지만 사람이 읽을 수는 없습니다. URI를 변경하는 경우 새 값이 고유한지 확인합니다. 애플리케이션 등록 포털에서 [구성된 게시자 도메인](howto-configure-publisher-domain.md)을 사용하는지 확인합니다.

클라이언트 애플리케이션에는 범위가 _위임 된 권한_ 으로 표시되고, 앱 역할은 웹 API에 대한 _애플리케이션 권한_ 으로 표시됩니다.

범위는 앱의 사용자에게 표시되는 동의 창에도 표시됩니다. 따라서 범위를 설명하는 해당 문자열을 제공합니다.

- 사용자에게 표시되는 항목
- 관리자 동의를 허용할 수 있는 테넌트 관리자에게 표시되는 항목

앱 역할은 사용자가 동의할 수 없습니다(자체적으로 사용되는 것이 아니라 웹 API를 호출하는 애플리케이션에서 사용되므로). 테넌트 관리자는 앱 역할을 노출하는 웹 API의 클라이언트 애플리케이션에 동의해야 합니다. 자세한 내용은 [관리자 동의](v2-admin-consent.md)를 참조하세요.

### <a name="exposing-delegated-permissions-scopes"></a>위임된 권한(범위) 노출

1. 애플리케이션 등록에서 **API 노출** 을 선택합니다.
1. **범위 추가** 를 선택합니다.
1. 메시지가 표시되면 **저장 후 계속** 을 선택하여 제안된 애플리케이션 ID URI(`api://{clientId}`)를 수락합니다.
1. 다음 값을 지정합니다.
   - **범위 이름** 을 선택하고 **access_as_user** 를 입력합니다.
   - **동의할 수 있는 사람** 을 선택하고 **관리자 및 사용자** 가 선택되어 있는지 확인합니다.
   - **관리자 동의 표시 이름** 을 선택하고 **TodoListService에 사용자로 액세스** 를 입력합니다.
   - **관리자 동의 설명** 을 선택하고 **TodoListService 웹 API에 사용자로 액세스** 를 입력합니다.
   - **사용자 동의 표시 이름** 을 선택하고 **TodoListService에 사용자로 액세스** 를 입력합니다.
   - **사용자 동의 설명** 을 선택하고 **TodoListService 웹 API에 사용자로 액세스** 를 입력합니다.
   - **상태** 값을 **사용** 으로 설정된 상태로 유지합니다.
1. **범위 추가** 를 선택합니다.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>디먼 앱에서 웹 API를 호출하는 경우

이 섹션에서는 디먼 앱이 안전하게 호출할 수 있도록 보호된 웹 API를 등록하는 방법에 대해 알아봅니다.

- 디먼 앱은 사용자와 상호 작용하지 않으므로 _애플리케이션 권한_ 만 선언하고 노출합니다. 위임된 권한은 적합하지 않습니다.
- 테넌트 관리자는 API의 애플리케이션 권한 중 하나에 액세스 하도록 등록된 애플리케이션에만 웹 API 토큰을 발급하도록 Azure AD에 요구할 수 있습니다.

#### <a name="exposing-application-permissions-app-roles"></a>애플리케이션 권한 노출(앱 역할)

애플리케이션 권한을 노출하려면 매니페스트를 편집합니다.

1. 애플리케이션의 애플리케이션 등록에서 **매니페스트** 를 선택합니다.
1. 매니페스트를 편집하려면 `appRoles` 설정을 찾고 애플리케이션 역할을 추가합니다. 역할 정의는 다음 샘플 JSON 블록에 제공됩니다.
1. `allowedMemberTypes`를 `"Application"`으로만 설정합니다.
1. `id`가 고유한 GUID인지 확인합니다.
1. `displayName` 및 `value`에 공백이 없는지 확인합니다.
1. 매니페스트를 저장합니다.

다음 샘플에서는 `appRoles`의 내용을 보여 줍니다. 여기서 `id`의 값은 임의의 고유 GUID가 될 수 있습니다.

```json
"appRoles": [
  {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
  }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD에서 허용된 클라이언트에만 웹 API에 대한 토큰을 발급하도록 보장

웹 API는 앱 역할을 확인합니다. 이 역할은 소프트웨어 개발자가 애플리케이션 권한을 노출하는 데 사용되는 방법입니다. 또한 테넌트 관리자가 API 액세스에 대해 승인한 앱에만 API 토큰을 발급하도록 Azure AD를 구성할 수 있습니다.

이 강화된 보안을 추가하려면 다음을 수행합니다.

1. 앱 등록에 대한 앱 **개요** 페이지로 이동합니다.
1. **로컬 디렉터리의 관리되는 애플리케이션** 에서 앱 이름이 포함된 링크를 선택합니다. 이 선택 항목의 레이블은 잘릴 수 있습니다. 예를 들어 **관리되는 애플리케이션 - ...** 같이 표시될 수 있습니다.

   이 링크를 선택하면 **엔터프라이즈 애플리케이션 개요** 페이지로 이동합니다. 이 페이지는 애플리케이션을 만든 테넌트에서 애플리케이션의 서비스 주체와 연결됩니다. 브라우저의 뒤로 단추를 사용하여 앱 등록 페이지로 이동할 수 있습니다.

1. 엔터프라이즈 애플리케이션 페이지의 **관리** 섹션에서 **속성** 페이지를 선택합니다.
1. Azure AD에서 특정 클라이언트에만 웹 API에 대한 액세스를 허용하도록 하려면 **사용자 할당 필요?** 를 **예** 로 설정합니다.

   
   **사용자 할당 필요?** 를 **예** 로 설정하면 Azure AD에서 웹 API 액세스 토큰을 요청할 때 클라이언트의 앱 역할 할당을 확인합니다. 클라이언트에 할당된 앱 역할이 없으면 Azure AD는 "invalid_client: AADSTS501051: \<application name\> 애플리케이션이 \<web API\> 역할에 할당되지 않았습니다"라는 오류 메시지를 반환합니다.
   
   **사용자 할당 필요?** 를 **아니요** 로 설정하면 클라이언트가 웹 API에 대한 액세스 토큰을 요청할 때 Azure AD에서 앱 역할 할당을 확인하지 않습니다. 모든 디먼 클라이언트, 즉 클라이언트 자격 증명 흐름을 사용하는 모든 클라이언트는 대상 그룹을 지정하는 방법으로만 API에 대한 액세스 토큰을 가져올 수 있습니다. 모든 애플리케이션은 이에 대한 권한을 요청하지 않고도 API에 액세스할 수 있습니다.
   
   그러나 이전 섹션에서 설명한 것처럼, 웹 API는 항상 애플리케이션이 테넌트 관리자에 의해 권한이 부여된 올바른 역할을 할당받았는지 확인할 수 있습니다. API는 액세스 토큰에 역할 클레임이 있고 이 클레임의 값이 올바른지 확인하는 방식으로 이 확인을 수행합니다. 이전 JSON 샘플에서 이 값은 `access_as_application`입니다.

1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 코드 구성](scenario-protected-web-api-app-configuration.md)으로 이동합니다.
