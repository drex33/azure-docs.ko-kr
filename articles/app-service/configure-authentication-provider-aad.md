---
title: Azure AD 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Azure Active Directory 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 10/26/2021
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: cc5761afe295bd9ef71c86cf6ffdafb8c0e847ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435836"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 앱이 인증 공급자로 Microsoft ID 플랫폼(Azure AD)를 사용하여 사용자를 로그인하도록 Azure App Service 또는 [Azure Functions](../active-directory/develop/v2-overview.md) 대한 인증을 구성하는 방법을 보여 줍니다.

App Service 인증 기능은 Microsoft ID 플랫폼 앱 등록을 자동으로 만들 수 있습니다. 사용자 또는 디렉터리 관리자가 별도로 만든 등록을 사용할 수도 있습니다.

- [자동으로 새 앱 등록 만들기](#express)
- [별도로 만든 기존 등록 사용](#advanced)

> [!NOTE]
> 정부 클라우드에서는 새 등록을 만드는 옵션을 사용할 수 없습니다. 대신 [등록을 별도로 정의](#advanced)합니다.

## <a name="option-1-create-a-new-app-registration-automatically"></a><a name="express"></a> 옵션 1: 자동으로 새 앱 등록 만들기

이 옵션은 인증 설정을 간소화하며 클릭 몇 번으로 진행할 수 있습니다.

1. [Azure Portal]에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽의 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. ID 공급자 드롭다운에서 **Microsoft** 를 선택합니다. 새 등록을 만드는 옵션이 기본적으로 선택되어 있습니다. 등록의 이름 또는 지원되는 계정 유형을 변경할 수 있습니다.

    클라이언트 암호는 `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET`라는 슬롯 고정 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 만들어지고 저장됩니다. Azure Key Vault에서 암호를 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.

1. 이것이 애플리케이션에 대해 구성된 첫 번째 ID 공급자인 경우 **App Service 인증 설정** 섹션도 표시됩니다. 그렇지 않으면 다음 단계로 이동할 수 있습니다.
    
    이러한 옵션은 애플리케이션이 인증되지 않은 요청에 응답하는 방법을 결정하며 기본 선택 항목은 이 새 공급자를 사용하여 로그인하도록 모든 요청을 리디렉션합니다. 지금 이 동작의 사용자 지정을 변경하거나, 나중에 기본 **인증** 화면에서 **인증 설정** 옆의 **편집** 을 선택하여 이 설정을 조정할 수 있습니다. 이러한 옵션에 대한 자세한 정보는 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. (선택 사항) **다음: 권한** 을 클릭하고 애플리케이션에 필요한 모든 범위를 추가합니다. 이러한 항목은 앱 등록에 추가되지만 나중에 변경할 수도 있습니다.
1. **추가** 를 클릭합니다.

이제 앱에서 인증에 Microsoft ID 플랫폼 사용할 준비가 되었습니다. 공급자는 **인증** 화면에 나열됩니다. 여기에서 공급자 구성을 편집하거나 삭제할 수 있습니다.

Azure Storage 및 Microsoft Graph에 액세스하는 웹앱에 대해 Azure AD 로그인을 구성하는 예제는 [이 자습서](scenario-secure-app-authentication-app-service.md)를 참조하세요.

## <a name="option-2-use-an-existing-registration-created-separately"></a><a name="advanced"> </a>옵션 2: 별도로 만든 기존 등록 사용

또한 등록을 사용자 지정하고 등록 세부 정보를 App Service 인증을 구성하여 Microsoft ID 플랫폼 애플리케이션을 수동으로 등록할 수도 있습니다. 이 방식은 예를 들어, 애플리케이션이 있는 것과는 다른 Azure AD 테넌트에서 앱 등록을 사용하려는 경우에 유용합니다.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Azure AD에서 App Service 앱의 앱 등록 만들기

먼저 앱 등록을 만듭니다. 이렇게 하려면 다음 정보를 수집합니다. 이 정보는 나중에 App Service 앱에서 인증을 구성할 때 필요합니다.

- 클라이언트 ID
- 테넌트 ID
- 클라이언트 암호(선택 사항)
- 애플리케이션 ID URI

앱을 등록하려면 다음 단계를 수행합니다.

1. [Azure Portal]에 로그인하고 **App Services** 를 검색하여 선택한 다음, 자신의 앱을 선택합니다. 앱 **URL** 을 적어 둡니다. Azure Active Directory 앱 등록을 구성할 때 이 정보를 사용합니다.
1. Portal 메뉴에서 **Azure Active Directory** 를 선택한 다음, **앱 등록** 탭으로 이동하여 **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 앱 등록의 **이름** 을 입력합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택하고 `<app-url>/.auth/login/aad/callback`를 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.
1. **등록** 을 선택합니다.
1. 앱 등록을 만든 후에는 나중에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 복사합니다.
1. **인증** 을 선택합니다. **암시적 허용 및 하이브리드 흐름에서** **ID 토큰을** 사용하도록 설정하여 OpenID 커넥트 사용자가 App Service 로그인할 수 있도록 합니다.  **저장** 을 선택합니다.
1. (선택 사항) **브랜딩** 을 선택합니다. **홈페이지 URL** 에서 App Service 앱의 URL을 입력하고 **저장** 을 선택합니다.
1. **API 노출을** 선택하고 "애플리케이션 ID URI" 옆에 있는 **설정을** 클릭합니다. 이 값은 리소스로 사용될 때 애플리케이션을 고유하게 식별하여 액세스 권한을 부여하는 토큰을 요청할 수 있도록 합니다. 만드는 범위에 대한 접두사로 사용됩니다.

    단일 테넌트 앱의 경우 형식의 기본값을 사용할 수 `api://<application-client-id>` 있습니다. 테넌트에서 확인된 도메인 중 하나를 기반으로 보다 읽기 가능한 URI를 지정할 수도 `https://contoso.com/api` 있습니다. 다중 테넌트 앱의 경우 사용자 지정 URI를 제공해야 합니다. 앱 ID URI에 허용되는 형식에 대한 자세한 내용은 [앱 등록 모범 사례 참조를 참조하세요.](../active-directory/develop/security-best-practices-for-app-registration.md#appid-uri-configuration)

    값을 입력한 후 **저장을** 클릭합니다.

1. **범위 추가** 를 선택합니다.
   1. **범위 추가에서** **애플리케이션 ID URI는** 이전 단계에서 설정한 값입니다.  **저장하고 계속** 을 선택합니다.
   1. **범위 이름** 에 *user_impersonation* 을 입력합니다.
   1. 텍스트 상자에는 동의 페이지에서 사용자에게 표시할 동의 범위 이름 및 설명을 입력합니다. 예를 들어 *Access &lt; &gt; application-name 을* 입력합니다.
   1. **범위 추가** 를 선택합니다.
1. (선택 사항) 클라이언트 암호를 만들려면 **인증서 & 비밀**  >  **클라이언트 비밀** 새 클라이언트  >  **암호** 를 선택합니다.  설명 및 만료를 입력하고 **추가를** 선택합니다. 페이지에 표시되는 클라이언트 암호 값을 복사합니다. 다시 표시되지 않습니다.
1. (선택 사항) **회신 URL** 을 여러 개 추가하려면 **인증** 을 선택합니다.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Azure Active Directory에서 Azure Active Directory 사용

1. [Azure Portal]에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽의 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. ID 공급자 드롭다운에서 **Microsoft** 를 선택합니다.
1. **앱 등록 유형** 의 경우 필요한 앱 정보를 자동으로 수집하는 **이 디렉터리에서 기존 앱 등록을 선택** 하도록 선택할 수 있습니다. 다른 테넌트의 등록이거나 등록 개체를 볼 수 있는 권한이 없는 경우 **기존 앱 등록의 세부 정보 제공** 을 선택합니다. 이 옵션의 경우 다음 구성 정보를 입력해야 합니다.

    |필드|설명|
    |-|-|
    |애플리케이션(클라이언트) ID| 앱 등록의 **애플리케이션(클라이언트) ID** 를 사용합니다. |
    |클라이언트 암호| 앱 등록에서 생성한 클라이언트 암호를 사용합니다. 클라이언트 비밀을 사용하면 하이브리드 흐름이 사용되며 App Service는 액세스 및 새로 고침 토큰을 반환합니다. 클라이언트 암호를 설정하지 않으면 암시적 흐름이 사용되며 ID 토큰만 반환됩니다. 이러한 토큰은 공급자가 보내고 EasyAuth 토큰 저장소에 저장됩니다.|
    |발급자 URL| `<authentication-endpoint>/<tenant-id>/v2.0`을 사용하고, \<authentication-endpoint>를 [클라우드 환경의 인증 엔드포인트](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)로 바꾸고(예: 글로벌 Azure는 "https://login.microsoftonline.com "), \<tenant-id> 를 앱 등록이 생성된 **디렉터리(테넌트) ID** 로 바꿉니다. 이 값은 사용자를 올바른 Azure AD 테넌트로 리디렉션하고 적절한 메타데이터를 다운로드하여 적절한 토큰 서명 키와 토큰 발급자 클레임 값을 확인하는 등의 용도로 사용됩니다. Azure AD v1을 사용하는 애플리케이션 및 Azure Functions 앱의 경우 URL에서 `/v2.0`을 생략합니다.|
    |허용되는 토큰 대상| 앱이 클라우드 또는 서버 앱이고 웹앱의 인증 토큰을 허용하려면 웹앱의 **애플리케이션 ID URI** 를 여기에 추가합니다. 구성된 **클라이언트 ID** 는 암시적으로 *항상* 허용되는 대상으로 간주됩니다.|

    클라이언트 비밀은 `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET`으로 명명된 슬롯 고정이란 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 저장됩니다. Azure Key Vault에서 비밀을 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.

1. 이것이 애플리케이션에 대해 구성된 첫 번째 ID 공급자인 경우 **App Service 인증 설정** 섹션도 표시됩니다. 그렇지 않으면 다음 단계로 이동할 수 있습니다.
    
    이러한 옵션은 애플리케이션이 인증되지 않은 요청에 응답하는 방법을 결정하며 기본 선택 항목은 이 새 공급자를 사용하여 로그인하도록 모든 요청을 리디렉션합니다. 지금 이 동작의 사용자 지정을 변경하거나, 나중에 기본 **인증** 화면에서 **인증 설정** 옆의 **편집** 을 선택하여 이 설정을 조정할 수 있습니다. 이러한 옵션에 대한 자세한 정보는 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **추가** 를 클릭합니다.

이제 앱에서 인증에 Microsoft ID 플랫폼 사용할 준비가 되었습니다. 공급자는 **인증** 화면에 나열됩니다. 여기에서 공급자 구성을 편집하거나 삭제할 수 있습니다.

## <a name="configure-client-apps-to-access-your-app-service"></a>App Service에 액세스하도록 클라이언트 앱 구성

이전 섹션에서는 사용자를 인증하기 위해 App Service 또는 Azure 함수를 등록했습니다. 이 섹션에서는 사용자 또는 자기 자신을 대신하여 App Service에서 노출하는 API에 대한 액세스를 요청할 수 있도록 네이티브 클라이언트 또는 디먼 앱을 등록하는 방법을 설명합니다. 사용자를 인증하기만 하려는 경우에는 이 섹션의 단계를 완료하지 않아도 됩니다.

### <a name="native-client-application"></a>네이티브 클라이언트 애플리케이션

로그인한 사용자를 대신하여 App Service 앱의 API에 대한 액세스를 요청하도록 네이티브 클라이언트를 등록할 수 있습니다.

1. [Azure portal]에서 **Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 앱 등록의 **이름** 을 입력합니다.
1. **리디렉션 URI** 에서 **퍼블릭 클라이언트(모바일 및 데스크톱)** 를 선택하고 URL로 `<app-url>/.auth/login/aad/callback`을 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.

    > [!NOTE]
    > Microsoft Store 애플리케이션의 경우 URI로 [패키지 SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid)를 대신 사용합니다.
1. **만들기** 를 선택합니다.
1. 앱 등록을 만든 후에는 **애플리케이션(클라이언트) ID** 의 값을 복사합니다.
1. **API 사용 권한** > **사용 권한 추가** > **내 API** 를 선택합니다.
1. 앞에서 App Service 앱에 대해 만든 앱 등록을 선택합니다. 앱 등록이 표시되지 않으면 [Azure AD에서 App Service 앱의 앱 등록 만들기](#register)에서 **user_impersonation** 범위를 추가했는지 확인합니다.
1. **위임된 권한** 에서 **user_impersonation** 을 선택한 다음, **사용 권한 추가** 를 선택합니다.

사용자 대신 App Service 앱에 액세스하도록 요청할 수 있는 네이티브 클라이언트 애플리케이션 구성이 완료되었습니다.

### <a name="daemon-client-application-service-to-service-calls"></a>디먼 클라이언트 애플리케이션(서비스 간 호출)

애플리케이션은 App Service 또는 애플리케이션 대신(사용자 대신이 아님) 함수 앱에서 호스트되는 웹 API를 호출하는 토큰을 획득할 수 있습니다. 이 시나리오는 로그인한 사용자가 없는 상태에서 작업을 수행하는 비대화형 디먼 애플리케이션에 유용합니다. 이 파일은 표준 OAuth 2.0 [클라이언트 자격 증명](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) 권한 부여를 사용합니다.

1. [Azure portal]에서 **Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 디먼 앱 등록의 **이름** 을 입력합니다.
1. 디먼 애플리케이션의 경우에는 리디렉션 URI가 필요하지 않으므로 비워 둘 수 있습니다.
1. **만들기** 를 선택합니다.
1. 앱 등록을 만든 후에는 **애플리케이션(클라이언트) ID** 의 값을 복사합니다.
1. **인증서 및 암호** > **새 클라이언트 암호** > **추가** 를 선택합니다. 페이지에 표시되는 클라이언트 암호 값을 복사합니다. 다시 표시되지 않습니다.

이제 `resource` 매개 변수를 대상 앱의 **애플리케이션 ID URI** 로 설정하여 [클라이언트 ID 및 클라이언트 암호를 사용하여 액세스 토큰을 요청](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)할 수 있습니다. 그런 다음, 결과적인 액세스 토큰은 표준 [OAuth 2.0 인증 헤더](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)를 사용하여 대상 앱에 제공될 수 있으며, App Service 인증/권한 부여는 일반적인 방법으로 토큰의 유효성을 검사하고 사용하여 이제 호출자(이 경우에는 사용자가 아닌 애플리케이션)가 인증되었음을 표시합니다.

현재 이 기능을 사용하면 Azure AD 테넌트의 ‘모든’ 클라이언트 애플리케이션이 액세스 토큰을 요청하고 대상 앱에 대한 인증을 수행할 수 있습니다. 특정 클라이언트 애플리케이션만 허용하도록 ‘권한 부여’를 적용하려면 몇 가지 추가 구성을 수행해야 합니다.

1. 보호하려는 App Service 또는 함수 앱을 나타내는 앱 등록의 매니페스트에서 [앱 역할을 정의합니다](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. 권한이 부여되어야 하는 클라이언트를 나타내는 앱 등록에서 **API 사용 권한** > **사용 권한 추가** > **내 API** 를 선택합니다.
1. 이전에 만든 앱 등록을 선택합니다. 앱 등록이 표시되지 않으면 [앱 역할을 추가](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)했는지 확인합니다.
1. **애플리케이션 사용 권한** 에서 이전에 만든 앱 역할을 선택하고 **사용 권한 추가** 를 선택합니다.
1. 권한을 요청하도록 클라이언트 애플리케이션에 권한을 부여하려면 **관리자 동의 부여** 를 클릭해야 합니다.
1. 이전 시나리오(어떤 역할도 추가되기 전)와 마찬가지로 이제 동일한 대상 `resource`에 대한 [액세스 토큰을 요청](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)하면, 액세스 토큰에는 클라이언트 애플리케이션에 대한 권한이 부여된 앱 역할을 포함하는 `roles` 클레임이 포함됩니다.
1. 이제 대상 App Service 또는 함수 앱 코드 내에서 예상한 역할이 토큰에 있는지 확인할 수 있습니다(App Service 인증/권한 부여에 의해 수행되지 않음). 자세한 내용은 [사용자 클레임 액세스](configure-authentication-user-identities.md#access-user-claims-in-app-code)를 참조하세요.

자체 ID를 사용하는 App Service 앱에 액세스할 수 있는 디먼 클라이언트 애플리케이션 구성이 완료되었습니다.

## <a name="best-practices"></a>모범 사례

인증을 설정하는 데 사용하는 구성에 관계 없이 다음 모범 사례를 통해 테넌트 및 애플리케이션의 보안을 유지할 수 있습니다.

- 각 App Service 앱에 고유한 권한 및 동의를 제공합니다.
- 각 App Service 앱을 각각 고유한 등록으로 구성합니다.
- 환경 간에 권한을 공유하는 일이 없도록 배포 슬롯마다 별도의 앱 등록을 사용합니다. 이렇게 하면 새 코드를 테스트할 때 문제가 프로덕션 앱에 영향을 주는 것을 방지할 수 있습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [자습서: Azure Storage 및 Microsoft Graph에 액세스하는 웹앱에서 사용자 인증 및 권한 부여](scenario-secure-app-authentication-app-service.md)
* [자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
