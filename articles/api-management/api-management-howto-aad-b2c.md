---
title: Azure Active Directory B2C를 사용하여 개발자 계정에 권한 부여
titleSuffix: Azure API Management
description: Azure Active Directory B2C를 사용하여 Azure API Management에서 개발자 포털의 사용자에게 권한을 부여하는 방법 알아보기
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 07/07/2021
ms.author: apimpm
ms.openlocfilehash: 6385f87eec00820d535845df21a44965e1708a55
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568158"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory B2C를 사용하여 개발자 계정에 권한을 부여하는 방법


Azure Active Directory B2C는 소비자 지향 웹 및 모바일 애플리케이션을 위한 클라우드 ID 관리 솔루션입니다. 이를 사용하여 API Management 개발자 포털에 대한 액세스를 관리할 수 있습니다. 

이 가이드에서는 Azure Active Directory B2C와 통합하려는 API Management 서비스에 필요한 구성을 보여 줍니다. 더 이상 사용되지 않는 레거시 개발자 포털을 사용하는 경우 이 문서에 설명된 대로 일부 단계가 다릅니다.

클래식 Azure Active Directory를 사용하여 개발자 포털에 대한 액세스를 사용하는 방법에 대한 정보는 [Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법](api-management-howto-aad.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 애플리케이션을 만들 Azure Active Directory B2C 테넌트. 자세한 내용은 [Azure Active Directory B2C 개요](../active-directory-b2c/overview.md)를 참조하세요.
* API Management 서비스가 아직 없는 경우 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 빠른 시작을 완료합니다.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>등록 및 로그인 사용자 흐름 구성

이 섹션에서는 등록 및 로그인 정책을 모두 포함하는 Azure Active Directory B2C 테넌트에 사용자 흐름을 만듭니다. 자세한 단계는 [Azure Active Directory B2C에서 사용자 흐름 및 사용자 지정 정책 만들기](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 Azure Active Directory B2C 테넌트에 액세스합니다.
1. **정책** 에서 **사용자 흐름** >  **+ 새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 탭에서 **가입 및 로그인** 사용자 흐름을 선택합니다.
1. 다음 정보를 지정합니다.
    1. 사용자 흐름의 고유한 이름을 입력합니다.
    1. **ID 공급자** 에서 **이메일 등록** 을 선택합니다.
    1. **사용자 속성 및 토큰 클레임** 에서 API Management 개발자 포털에 필요한 속성 및 클레임을 선택합니다(기존 개발자 포털에는 필요하지 않음).
         ![애플리케이션 클레임](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * **속성**: 이름, 성
        * **클레임**: 이메일 주소, 이름, 성, 사용자 ObjectID
1. **만들기** 를 선택합니다.

## <a name="configure-identity-provider-for-developer-portal"></a>개발자 포털에 대한 ID 공급자 구성

1. 별도의 [Azure Portal](https://portal.azure.com) 탭에서 API Management 인스턴스로 이동합니다.
1. **개발자 포털** 에서 **ID** >  **+ 추가** 를 선택합니다.
1. **ID 공급자 추가** 페이지에서 **Azure Active Directory B2C** 를 선택합니다.
1. **ID 공급자 추가** 창에서 **리디렉션 URL** 을 복사합니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="리디렉션 URL 복사":::    

1. Azure Portal에서 Azure Active Directory B2C 테넌트의 브라우저 탭으로 돌아갑니다. **앱 등록** >   **+ 새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지에서 애플리케이션의 등록 정보를 입력합니다.
    * **이름** 섹션에서 선택한 애플리케이션 이름을 입력합니다.
    * **지원되는 계정 유형** 섹션에서 시나리오에 적합한 계정 유형을 선택합니다. 광범위한 고객을 대상으로 하려면 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 자세한 내용은 [애플리케이션 등록](../active-directory/develop/quickstart-register-app.md#register-an-application)을 참조하세요.
    * **리디렉션 URI** 에 API Management 인스턴스에서 복사한 리디렉션 URL을 입력합니다.
    * **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 를 선택합니다.
    * **등록** 을 선택하여 애플리케이션을 만듭니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="새 애플리케이션 등록":::

1. 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 찾아 값을 클립보드에 복사합니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="애플리케이션 ID":::
1. API Management **ID 공급자 추가** 페이지로 다시 전환하고 ID를 **클라이언트 ID** 텍스트 상자에 붙여 넣습니다.
1. B2C 앱 등록으로 다시 전환합니다. **인증서 및 비밀** >  **+ 새 클라이언트 암호** 를 선택합니다. 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="클라이언트 비밀 생성"::: 
   * **클라이언트 암호 추가** 페이지에서 **설명** 을 입력하고 **추가** 를 선택합니다.
   * 안전한 위치에 키를 기록합니다. 이 비밀 값은 이 페이지에서 나가면 다시 표시되지 않습니다.
1. API Management **ID 공급자 추가** 페이지로 다시 전환하고 키를 **클라이언트 암호** 텍스트 상자에 붙여 넣습니다.
1. B2C 앱 등록으로 다시 전환합니다. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다.
    * **암시적 허용** 에서 **액세스 토큰** 확인란을 선택합니다.
    * **저장** 을 선택합니다.
1. API Management **ID 공급자 추가** 페이지에서 다시 전환합니다.
    * **로그인 테넌트** 에서 Azure Active Directory B2C 테넌트의 도메인 이름을 지정합니다.
    * **권한** 필드를 사용하여 사용할 Azure AD B2C 로그인 URL을 제어할 수 있습니다. 값을 **<your_b2c_tenant_name>.b2clogin.com** 으로 설정합니다.
    * B2C 테넌트 정책에서 **등록 정책** 및 **로그인 정책** 을 지정합니다.
    * 선택적으로 **프로필 편집 정책** 및 **암호 재설정 정책** 을 제공합니다.

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Active Directory B2C ID 공급자 구성":::
1. 원하는 구성이 지정되면 **추가** 를 선택합니다.

변경 내용이 저장되면 개발자는 Azure Active Directory B2C를 사용하여 새 계정을 만들고 개발자 포털에 로그인할 수 있습니다.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>개발자 포털 - Azure AD B2C 계정 인증 추가

> [!IMPORTANT]
> 변경 사항을 적용하려면 Azure Active Directory B2C 구성 설정을 만들거나 업데이트할 때 [개발자 포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish)해야 합니다.

개발자 포털에서는 **로그인 단추: OAuth** 위젯을 사용하여 Azure AD B2C로 로그인합니다. 위젯은 기본 개발자 포털 콘텐츠의 로그인 페이지에 이미 포함되어 있습니다.

1. Azure Active Directory B2C를 사용하여 로그인하려면 새 브라우저 창을 열고 개발자 포털로 이동합니다. **로그인** 을 선택합니다.

1. **로그인** 페이지에서 **Azure Active Directory B2C** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="개발자 포털에 로그인":::
1. 이전 섹션에서 구성하였던 등록 정책으로 리디렉션됩니다. Active Directory B2C 테넌트의 이메일 주소를 사용하여 등록하도록 선택

등록이 완료되면 개발자 포털로 다시 리디렉션됩니다. 이제 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="개발자 포털에 로그인 완료":::

새 사용자가 Azure AD B2C로 로그인할 때마다 새 계정이 자동으로 생성되지만, 동일한 위젯을 등록 페이지에 추가하는 것이 좋습니다.

**등록 양식: OAuth** 위젯은 OAuth로 등록하는 데 사용되는 양식을 나타냅니다.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>레거시 개발자 포털 - Azure AD B2C에 등록하는 방법

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Azure AD B2C를 사용하여 개발자 계정에 등록하려면 새 브라우저 창을 열고 레거시 개발자 포털로 이동합니다. **등록** 단추를 클릭합니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="기존 개발자 포털에 등록":::
1. **Azure Active Directory B2C** 를 사용하여 등록하도록 선택합니다.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="Azure Active Directory B2C를 사용하여 등록":::

3. 이전 섹션에서 구성하였던 등록 정책으로 리디렉션됩니다. 전자 메일 주소 또는 기존 소셜 계정 중 하나를 사용하여 등록하도록 선택합니다.

   > [!NOTE]
   > Azure Active Directory B2C가 게시자 포털의 **ID** 탭에서 사용하도록 설정되는 유일한 옵션인 경우 등록 정책에 직접 리디렉션됩니다.

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="기존 개발자 포털의 등록 옵션":::

   등록이 완료되면 개발자 포털로 다시 리디렉션됩니다. 이제 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

## <a name="next-steps"></a>다음 단계

*  [Azure Active Directory B2C 개요]
*  [Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]
*  [Azure Active Directory B2C에서 Microsoft 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Google 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 LinkedIn 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Facebook 계정을 ID 공급자로 사용]


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
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
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
