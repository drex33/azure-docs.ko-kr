---
title: Azure Active Directory를 사용하여 개발자 계정에 권한 부여
titleSuffix: Azure API Management
description: API Management에서 Azure Active Directory를 사용하여 사용자에게 권한을 부여하는 방법을 알아봅니다.
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: 03ac79a70a1725fd6d1ceca6d79d4cdb325a8c51
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428467"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여

이 문서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * azure AD(Azure Active Directory)의 사용자가 개발자 포털에 액세스할 수 있도록 설정합니다.
> * 사용자를 포함하는 외부 그룹을 추가하여 Azure AD 사용자 그룹을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 빠른 시작을 완료합니다.
- Azure API Management [인스턴스를 가져오고 게시합니다.](import-and-publish.md)

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD를 사용하여 개발자 계정에 권한 부여

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 선택 ![화살표 아이콘을 선택합니다.](./media/api-management-howto-aad/arrow.png).
1. **API Management 서비스** 를 검색하고 선택합니다.
1. API Management 서비스 인스턴스를 선택합니다.
1. **개발자 포털** 에서 **ID** 를 선택합니다.
1. 위쪽에서 **+추가를** 선택하여 오른쪽에 **있는 ID 공급자 추가** 창을 엽니다.
1. **형식 아래의** 드롭다운 메뉴에서 **Azure Active Directory** 선택합니다.
    * 선택되면 다른 필요한 정보를 입력할 수 있습니다. 
    * 정보에는 **클라이언트 ID** 및 클라이언트 **암호** 가 포함됩니다. 
    * 이러한 컨트롤에 대한 자세한 내용은 문서의 후반부를 참조하세요.
1. 나중에 사용할 수 있는 **리디렉션 URL을** 저장합니다.
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Azure Portal에 ID 공급자 추가":::

    > [!NOTE]
    > 리디렉션 URL에는 다음 두 가지가 있습니다.<br/>
    > * **리디렉션 URL은** API Management 최신 개발자 포털을 가리킵니다.
    > * **리디렉션 URL(사용되지 않은 포털)은** 사용되지 API Management 개발자 포털을 가리킵니다.
    >
    > 최신 개발자 포털 리디렉션 URL을 사용하는 것이 좋습니다.
   
1. 브라우저의 새 탭에서 Azure Portal 엽니다. 
1. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 이동하여 Active Directory에 앱을 등록합니다.
1. **새 등록** 을 선택합니다. **애플리케이션 등록** 페이지에서 다음과 같이 값을 설정합니다.
    
    * **Name** 을 의미 있는 이름으로 설정합니다. 예: *developer-portal*
    * **지원되는 계정 유형** 을 **이 조직 디렉터리에만 있는 계정** 으로 설정합니다. 
    * **리디렉션 URI를** 9단계에서 저장한 값으로 설정합니다. 
    * **등록** 을 선택합니다. 

1.  애플리케이션을 등록한 후 **개요** 페이지에서 **애플리케이션(클라이언트) ID를** 복사합니다. 
1. API Management 인스턴스가 있는 브라우저 탭으로 전환합니다. 
1. **ID 공급자 추가** 창의 **클라이언트 ID** 상자에 **애플리케이션(클라이언트) ID** 값을 붙여넣습니다.
1. 앱 등록을 사용하여 브라우저 탭으로 전환합니다.
1. 적절한 앱 등록을 선택합니다.
1. 측면 메뉴의 **관리** 섹션에서 **인증서 & 비밀을** 선택합니다. 
1. 인증서 **& 비밀** 페이지의 클라이언트 비밀 아래에서 **새 클라이언트** **암호** 단추를 선택합니다. 
    * **설명** 을 입력합니다.
    * **만료에** 대한 옵션을 선택합니다.
    * **추가** 를 선택합니다. 
1. 페이지를 벗어나기 전에 클라이언트 **비밀 ID를** 복사합니다. 이 시간은 나중에 필요합니다. 
1. 측면 메뉴의 **관리에서** **인증을** 선택합니다.
1. **암시적 허용 및 하이브리드 흐름** 섹션에서 ID 토큰 확인란을 선택합니다. 
1. API Management 인스턴스가 있는 브라우저 탭으로 전환합니다. 
1. **ID 공급자 추가** 창의 클라이언트 암호 필드에 **비밀을** 붙여넣습니다.

    > [!IMPORTANT]
    > 키가 만료되기 전에 **클라이언트 비밀을** 업데이트합니다. 

1. ID **공급자 추가** 창의 **허용된 테넌트** 필드에서 API Management 서비스 인스턴스 API에 대한 액세스 권한을 부여할 Azure AD 인스턴스의 도메인을 지정합니다. 
    * 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

    > [!NOTE]
    > **허용된 테넌트** 섹션에서 여러 도메인을 지정할 수 있습니다. 전역 관리는 사용자가 원래 앱 등록 도메인이 아닌 다른 도메인에서 로그인할 수 있도록 애플리케이션에 디렉터리 데이터에 대한 액세스 권한을 부여해야 합니다. 사용 권한을 부여하려면 전역 관리자는 다음을 수행해야 합니다.
    > 1. 로 `https://<URL of your developer portal>/aadadminconsent` 이동합니다(예: `https://contoso.portal.azure-api.net/aadadminconsent` ).
    > 1. 액세스 권한을 부여할 Azure AD 테넌트 도메인 이름을 입력합니다.
    > 1. **제출** 을 선택합니다. 

1.  원하는 구성을 지정한 후에 **추가** 를 선택합니다.

변경 내용이 저장되면 지정된 Azure AD 인스턴스의 사용자는 [Azure AD 계정 을 사용하여 개발자 포털에 로그인할](#log_in_to_dev_portal)수 있습니다.

## <a name="add-an-external-azure-ad-group"></a>외부 Azure AD 그룹 추가

이제 Azure AD 테넌트의 사용자에 대한 액세스를 사용하도록 설정했으므로 다음을 수행할 수 있습니다.
* azure AD 그룹을 API Management 추가합니다. 
* Azure AD 그룹을 사용하여 제품 표시를 제어합니다.

기본적으로 [이전 섹션에서](#authorize-developer-accounts-by-using-azure-ad) 등록한 애플리케이션은 필요한 위임된 권한으로 Microsoft Graph API에 액세스할 `User.Read` 수 있습니다. 다음 단계에 따라 애플리케이션 권한을 사용하여 애플리케이션에 Microsoft Graph API 및 Azure AD Graph API에 대한 `Directory.Read.All` 액세스 권한을 부여합니다. 

1. 이전 섹션에서 만든 앱 등록으로 이동합니다.
2. 측면 메뉴의 **관리에서** **API 권한 을 선택합니다.**
1. **사용 권한 추가** 를 선택합니다. 
1. API 권한 요청 창에서 **다음을 수행합니다.**
    1. Microsoft **API** 탭을 선택합니다.
    1. Microsoft **Graph** 타일을 선택합니다. 
    1. **애플리케이션 권한** 을 선택하고 **디렉터리** 를 검색합니다. 
    1. **Directory.Read.All** 권한을 선택합니다. 
    1. 창 아래쪽에서 **권한 추가를** 선택합니다.
1. **사용 권한 추가를** 선택하여 다른 권한을 추가합니다. 
1. API 권한 요청 창에서 **다음을 수행합니다.**
    1. Microsoft **API** 탭을 선택합니다.
    1. **지원되는 레거시 API** 섹션으로 Scroll down.
    1. **Azure Active Directory Graph** 타일을 선택합니다. 
    1. **애플리케이션 권한** 을 선택하고 **디렉터리** 를 검색합니다. 
    1. **Directory.Read.All** 권한을 선택합니다.
    1. **권한 추가** 를 선택합니다. 
1. 이 디렉터리의 모든 사용자에 게 액세스 권한을 부여하도록 **{tenantname}에 대한 관리자 동의 부여** 를 선택합니다. 

이제 API Management 인스턴스의 **그룹** 탭에서 외부 Azure AD 그룹을 추가할 수 있습니다.

1. 측면 메뉴의 **개발자 포털** 아래에서 **그룹** 을 선택합니다.
1. Azure **AD 그룹 추가** 단추를 선택합니다.

   !["A A D 그룹 추가" 단추](./media/api-management-howto-aad/api-management-with-aad008.png)
1. 드롭다운에서 **테넌트** 를 선택합니다. 
1. 추가하려는 그룹을 검색하여 선택합니다.
1. **선택** 단추를 누릅니다.

외부 Azure AD 그룹을 추가하면 해당 속성을 검토하고 구성할 수 있습니다. 
1. 그룹의 이름을 **그룹** 탭에서 선택합니다. 
1. 그룹의 **이름** 및 **설명** 정보를 편집합니다.
 
구성된 Azure AD 인스턴스의 사용자는 이제 다음을 수행할 수 있습니다.
* 개발자 포털에 로그인합니다. 
* 표시 유형이 있는 그룹을 보고 구독합니다.

> [!NOTE]
> Microsoft ID 플랫폼 문서에서 사용 권한 및 동의에서 **위임된** 권한 유형과 **애플리케이션** 권한 유형의 [차이점에](../active-directory/develop/v2-permissions-and-consent.md#permission-types) 대해 자세히 알아봅니다.

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> 개발자 포털: Azure AD 계정 인증 추가

개발자 포털에서 로그인 단추: 기본 개발자 포털 콘텐츠의 로그인 페이지에 포함된 **OAuth** 위젯을 사용하여 Azure AD로 로그인할 수 있습니다.

새 사용자가 Azure AD로 로그인할 때 새 계정이 자동으로 만들어지지만 등록 페이지에 동일한 위젯을 추가하는 것이 좋습니다. **등록 양식: OAuth** 위젯은 OAuth로 등록하는 데 사용되는 양식을 나타냅니다.

> [!IMPORTANT]
> Azure AD 변경 내용을 적용하려면 [포털을 다시 게시해야](api-management-howto-developer-portal-customize.md#publish) 합니다.

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>레거시 개발자 포털: Azure AD로 로그인하는 방법

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

이전 섹션에서 구성한 Azure AD 계정을 사용하여 개발자 포털에 로그인하려면 다음을 수행합니다.

1. Active Directory 애플리케이션 구성에서 로그인 URL을 사용하여 새 브라우저 창을 엽니다. 
1. **Azure Active Directory** 를 선택합니다.

   ![로그인 페이지][api-management-dev-portal-signin]

1. Azure AD에서 사용자 중 하나의 자격 증명을 입력합니다.
1. **로그인** 을 선택합니다.

   ![사용자 이름 및 암호로 로그인][api-management-aad-signin]

1. 등록 양식을 입력하라는 메시지가 표시되면 필요한 추가 정보를 입력합니다. 
1. **등록을** 선택합니다.

   ![등록 양식의 "등록" 단추][api-management-complete-registration]

이제 사용자는 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료 이후 개발자 포털][api-management-registration-complete]

## <a name="next-steps"></a>다음 단계

- [Azure AD에서 OAuth 2.0 권한 부여를 사용하여 API Management 웹 API 백 엔드를 보호하는](./api-management-howto-protect-backend-with-aad.md) 방법을 알아봅니다.
- [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)에 대해 자세히 알아봅니다.
- API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
- 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](./api-management-howto-mutual-certificates.md)을 참조하세요.
- [API Management 서비스 인스턴스 만들기](./get-started-create-service-instance.md)
- [첫 번째 API 관리](./import-and-publish.md)

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
