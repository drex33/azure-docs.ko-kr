---
title: Azure Active Directory B2C를 사용하여 개발자 계정에 권한 부여
titleSuffix: Azure API Management
description: API Management에서 Azure Active Directory B2C를 사용하여 사용자에게 권한을 부여하는 방법에 대해 알아보세요.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013567"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory B2C를 사용하여 개발자 계정에 권한을 부여하는 방법

## <a name="overview"></a>개요

Azure Active Directory B2C는 소비자 지향 웹 및 모바일 애플리케이션을 위한 클라우드 ID 관리 솔루션입니다. 개발자 포털에 대한 액세스 권한을 관리하는 데 사용할 수 있습니다. 이 가이드에서는 Azure Active Directory B2C와 통합하려는 API Management 서비스에 필요한 구성을 보여 줍니다. 클래식 Azure Active Directory를 사용하여 개발자 포털에 대한 액세스를 사용하는 방법에 대한 정보는 [Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법]을 참조하세요.

> [!NOTE]
> 이 가이드의 단계를 완료하려면 먼저 애플리케이션을 만들 Azure Active Directory B2C 테넌트가 있어야 합니다. 또한, 등록 및 로그인 정책이 준비되어야 합니다. 자세한 내용은 [Azure Active Directory B2C 개요]를 참조하세요.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 개발자 계정에 권한 부여

1. 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인하고 API Management 인스턴스를 찾습니다.

   > [!NOTE]
   > 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [Azure API Management 시작 자습서][Get started with Azure API Management]의 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

1. **ID** 아래에서. 위쪽에서 **+추가** 를 클릭합니다.

   **ID 공급자 추가** 창이 오른쪽에 나타납니다. **Azure Active Directory B2C** 를 선택합니다.
    
   ![AAD B2C를 ID 공급자로 추가][api-management-howto-add-b2c-identity-provider]

1. **리디렉션 URL** 을 복사합니다.

   ![AAD B2C ID 공급자 리디렉션 URL][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. 새 탭에서 Azure Portal의 Azure Active Directory B2C 테넌트에 액세스하고 **애플리케이션** 블레이드를 엽니다.

   ![새 애플리케이션 1 등록][api-management-howto-aad-b2c-portal-menu]

1. **추가** 단추를 클릭하여 새 Azure Active Directory B2C 애플리케이션을 만듭니다.

   ![새 애플리케이션 2 등록][api-management-howto-aad-b2c-add-button]

1. **새 애플리케이션** 블레이드에서 애플리케이션의 이름을 입력합니다. **Web App/Web API** 에서 **예** 를 선택하고 **암시적 흐름 허용** 에서 **예** 를 선택합니다. 그런 다음, 3단계에서 복사한 **리디렉션 URL** 을 **회신 URL** 텍스트 상자에 붙여 넣습니다.

   ![새 애플리케이션 3 등록][api-management-howto-aad-b2c-app-details]

1. 새 개발자 포털(레거시 개발자 포털이 아님)을 사용하는 경우 애플리케이션 클레임에 **이름**, **성**, **사용자의 개체 ID** 를 포함합니다.

    ![애플리케이션 클레임](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. **만들기** 단추를 클릭합니다. 애플리케이션이 만들어지면 **애플리케이션** 블레이드에 표시됩니다. 세부 정보를 보려면 애플리케이션 이름을 클릭합니다.

   ![새 애플리케이션 4 등록][api-management-howto-aad-b2c-app-created]

1. **속성** 블레이드에서 **애플리케이션 ID** 를 클립보드에 복사합니다.

   ![애플리케이션 ID 1][api-management-howto-aad-b2c-app-id]

1. API Management **ID 공급자 추가** 창으로 다시 전환하고 ID를 **클라이언트 ID** 텍스트 상자에 붙여 넣습니다.
    
1.  B2C 앱 등록으로 다시 전환하고 **키** 단추를 클릭한 다음, **키 생성** 을 클릭합니다. **저장** 을 클릭하여 구성을 저장하고 **앱 키** 를 표시합니다. 키를 클립보드에 복사합니다.

    ![앱 키 1][api-management-howto-aad-b2c-app-key]

1.  API Management **ID 공급자 추가** 창으로 다시 전환하고 키를 **클라이언트 비밀** 텍스트 상자에 붙여 넣습니다.
    
1.  **로그인 테넌트** 에서 Azure Active Directory B2C 테넌트의 도메인 이름을 지정합니다.

1.  **권한** 필드를 사용하여 사용할 Azure AD B2C 로그인 URL을 제어할 수 있습니다. 값을 **<your_b2c_tenant_name>.b2clogin.com** 으로 설정합니다.

1. B2C 테넌트 정책에서 **등록 정책** 및 **로그인 정책** 을 지정합니다. 선택적으로 **프로필 편집 정책** 및 **암호 재설정 정책** 을 제공할 수도 있습니다.

1. 원하는 구성이 지정되면 **저장** 을 클릭합니다.

    변경 내용이 저장되면 개발자는 Azure Active Directory B2C를 사용하여 새 계정을 만들고 개발자 포털에 로그인할 수 있습니다.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>개발자 포털 - Azure AD B2C 계정 인증 추가

개발자 포털에서는 **로그인 단추: OAuth** 위젯을 사용하여 AAD B2C로 로그인합니다. 위젯은 기본 개발자 포털 콘텐츠의 로그인 페이지에 이미 포함되어 있습니다.

새 사용자가 AAD B2C로 로그인할 때마다 새 계정이 자동으로 생성되지만, 동일한 위젯을 등록 페이지에 추가하는 것이 좋습니다.

**등록 양식: OAuth** 위젯은 OAuth로 등록하는 데 사용되는 양식을 나타냅니다.

> [!IMPORTANT]
> AAD 변경 내용을 적용하려면 [개발자 포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>레거시 개발자 포털 - Azure AD B2C에 등록하는 방법

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Azure Active Directory B2C를 사용하여 개발자 계정에 등록하려면 새 브라우저 창을 열고 개발자 포털로 이동합니다. **등록** 단추를 클릭합니다.

   ![개발자 포털 1][api-management-howto-aad-b2c-dev-portal]

2. **Azure Active Directory B2C** 를 사용하여 등록하도록 선택합니다.

   ![개발자 포털 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. 이전 섹션에서 구성하였던 등록 정책으로 리디렉션됩니다. 전자 메일 주소 또는 기존 소셜 계정 중 하나를 사용하여 등록하도록 선택합니다.

   > [!NOTE]
   > Azure Active Directory B2C가 게시자 포털의 **ID** 탭에서 사용하도록 설정되는 유일한 옵션인 경우 등록 정책에 직접 리디렉션됩니다.

   ![개발자 포털][api-management-howto-aad-b2c-dev-portal-b2c-options]

   등록이 완료되면 개발자 포털로 다시 리디렉션됩니다. 이제 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

    ![등록 완료][api-management-registration-complete]

## <a name="next-steps"></a>다음 단계

*  [Azure Active Directory B2C 개요]
*  [Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]
*  [Azure Active Directory B2C에서 Microsoft 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Google 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 LinkedIn 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Facebook 계정을 ID 공급자로 사용]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C 개요]: ../active-directory-b2c/overview.md
[Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법]: ./api-management-howto-aad.md
[Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]: ../active-directory-b2c/user-flow-overview.md
[Azure Active Directory B2C에서 Microsoft 계정을 ID 공급자로 사용]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Azure Active Directory B2C에서 Google 계정을 ID 공급자로 사용]: ../active-directory-b2c/identity-provider-google.md
[Azure Active Directory B2C에서 Facebook 계정을 ID 공급자로 사용]: ../active-directory-b2c/identity-provider-facebook.md
[Azure Active Directory B2C에서 LinkedIn 계정을 ID 공급자로 사용]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
