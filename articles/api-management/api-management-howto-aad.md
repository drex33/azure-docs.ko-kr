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
ms.openlocfilehash: ae043f6765c44ddee8fb6816017937af10413a76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474044"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여

이 문서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Active Directory (Azure AD)의 사용자에 대 한 개발자 포털 액세스를 사용 하도록 설정 합니다.
> * 사용자를 포함 하는 외부 그룹을 추가 하 여 Azure AD 사용자 그룹을 관리 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 빠른 시작을 완료합니다.
- Azure API Management 인스턴스를 [가져오고 게시](import-and-publish.md) 합니다.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD를 사용하여 개발자 계정에 권한 부여

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 선택 ![화살표 아이콘을 선택합니다.](./media/api-management-howto-aad/arrow.png).
1. **API Management 서비스** 를 검색하고 선택합니다.
1. API Management 서비스 인스턴스를 선택합니다.
1. **개발자 포털** 에서 **ID** 를 선택합니다.
1. 위쪽에서 **+ 추가** 를 선택 하 여 오른쪽에 있는 **id 공급자 추가** 창을 엽니다.
1. **유형** 아래에 있는 드롭다운 메뉴에서 **Azure Active Directory** 를 선택 합니다.
    * 이를 선택 하면 다른 필요한 정보를 입력할 수 있습니다. 
    * 정보에는 **클라이언트 ID** 및 **클라이언트 암호가** 포함 됩니다. 
    * 이러한 컨트롤에 대 한 자세한 내용은이 문서의 뒷부분에 나와 있습니다.
1. 나중에에 대 한 **리디렉션 URL** 을 저장 합니다.
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Azure Portal에 ID 공급자 추가":::

    > [!NOTE]
    > 리디렉션 URL에는 다음 두 가지가 있습니다.<br/>
    > * **URL** 지점은 API Management의 최신 개발자 포털로 리디렉션됩니다.
    > * **리디렉션 URL (사용 되지 않는 포털)** 은 API Management의 사용 되지 않는 개발자 포털을 가리킵니다.
    >
    > 최신 개발자 포털 리디렉션 URL을 사용 하는 것이 좋습니다.
   
1. 브라우저에서 새 탭의 Azure Portal을 엽니다. 
1. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 로 이동 하 여 Active Directory에 앱을 등록 합니다.
1. **새 등록** 을 선택합니다. **애플리케이션 등록** 페이지에서 다음과 같이 값을 설정합니다.
    
    * **Name** 을 의미 있는 이름으로 설정합니다. 예: *developer-portal*
    * **지원되는 계정 유형** 을 **이 조직 디렉터리에만 있는 계정** 으로 설정합니다. 
    * **리디렉션 URI** 를 9 단계에서 저장 한 값으로 설정 합니다. 
    * **등록** 을 선택합니다. 

1.  응용 프로그램을 등록 한 후 **개요** 페이지에서 **응용 프로그램 (클라이언트) ID** 를 복사 합니다. 
1. API Management 인스턴스를 사용 하 여 브라우저 탭으로 전환 합니다. 
1. **ID 공급자 추가** 창의 **클라이언트 ID** 상자에 **애플리케이션(클라이언트) ID** 값을 붙여넣습니다.
1. 앱 등록을 사용 하 여 브라우저 탭으로 전환 합니다.
1. 적절 한 앱 등록을 선택 합니다.
1. 측면 메뉴의 **관리** 섹션 아래에서 **인증서 & 암호** 를 선택 합니다. 
1. **인증서 & 암호** 페이지에서 **클라이언트** 암호 아래에 있는 **새 클라이언트 암호** 단추를 선택 합니다. 
    * **설명** 을 입력합니다.
    * **만료** 에 대 한 옵션을 선택 합니다.
    * **추가** 를 선택합니다. 
1. 페이지를 벗어나기 전에 클라이언트 **암호 ID** 를 복사 합니다. 이 시간은 나중에 필요합니다. 
1. 측면 메뉴의 **관리** 에서 **인증** 을 선택 합니다.
1. **암시적 허용 및 하이브리드 흐름** 섹션에서 **ID 토큰** 확인란을 선택 합니다.
1. API Management 인스턴스를 사용 하 여 브라우저 탭으로 전환 합니다. 
1. **Id 공급자 추가** 창의 **클라이언트 암호** 필드에 비밀을 붙여넣습니다.

    > [!IMPORTANT]
    > 키가 만료 되기 전에 **클라이언트 암호** 를 업데이트 합니다. 

1. **Id 공급자** 창의 **허용 된 테 넌 트** 추가 필드에서 API Management 서비스 인스턴스 api에 대 한 액세스 권한을 부여 하려는 Azure AD 인스턴스 도메인을 지정 합니다. 
    * 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

    > [!NOTE]
    > **허용된 테넌트** 섹션에서 여러 도메인을 지정할 수 있습니다. 사용자가 원래 앱 등록 도메인과 다른 도메인에서 로그인 하려면 전역 관리에서 디렉터리 데이터에 대 한 액세스 권한을 응용 프로그램에 부여 해야 합니다. 사용 권한을 부여하려면 전역 관리자는 다음을 수행해야 합니다.
    > 1. 로 이동 `https://<URL of your developer portal>/aadadminconsent` 합니다 (예: `https://contoso.portal.azure-api.net/aadadminconsent` ).
    > 1. 액세스 권한을 부여 하려는 Azure AD 테 넌 트의 도메인 이름을 입력 합니다.
    > 1. **제출** 을 선택합니다. 

1.  원하는 구성을 지정한 후에 **추가** 를 선택합니다.

변경 내용이 저장 되 면 지정 된 Azure AD 인스턴스의 사용자는 [AZURE ad 계정을 사용 하 여 개발자 포털에 로그인](#log_in_to_dev_portal)할 수 있습니다.

## <a name="add-an-external-azure-ad-group"></a>외부 Azure AD 그룹 추가

이제 Azure AD 테 넌 트의 사용자에 대 한 액세스를 사용 하도록 설정 했으므로 다음을 수행할 수 있습니다.
* API Management에 Azure AD 그룹을 추가 합니다. 
* Azure AD 그룹을 사용 하 여 제품 표시 여부를 제어 합니다.

다음 단계를 수행 하 여 권한을 부여 합니다.
* `Directory.Read.All`Microsoft Graph API 및 Azure Active Directory Graph API에 대 한 응용 프로그램 권한.
* `User.Read`Microsoft Graph API에 대 한 위임 된 권한입니다. 

1. 다음 PowerShell 스크립트의 처음 3 줄을 사용자 환경에 맞게 업데이트 하 고 실행 합니다. 
   ```powershell
   $subId = "Your Azure subscription ID" #e.g. "1fb8fadf-03a3-4253-8993-65391f432d3a"
   $tenantId = "Your Azure AD Tenant or Organization ID" #e.g. 0e054eb4-e5d0-43b8-ba1e-d7b5156f6da8"
   $appObjectID = "Application Object ID that has been registered in AAD" #e.g. "2215b54a-df84-453f-b4db-ae079c0d2619"
   #Login and Set the Subscription
   az login
   az account set --subscription $subId
   #Assign the following permissions: Microsoft Graph Delegated Permission: User.Read, Microsoft Graph Application Permission: Directory.ReadAll,  Azure Active Directory Graph Application Permission: Directory.ReadAll (legacy)
   az rest --method PATCH --uri "https://graph.microsoft.com/v1.0/$($tenantId)/applications/$($appObjectID)" --body "{'requiredResourceAccess':[{'resourceAccess': [{'id': 'e1fe6dd8-ba31-4d61-89e7-88639da4683d','type': 'Scope'},{'id': '7ab1d382-f21e-4acd-a863-ba3e13f7da61','type': 'Role'}],'resourceAppId': '00000003-0000-0000-c000-000000000000'},{'resourceAccess': [{'id': '5778995a-e1bf-45b8-affa-663a9f3f4d04','type': 'Role'}], 'resourceAppId': '00000002-0000-0000-c000-000000000000'}]}"
   ```
2. 로그 아웃 했다가 다시 로그인 하 여 Azure Portal 합니다.
3. [이전 섹션](#authorize-developer-accounts-by-using-azure-ad)에서 등록 한 응용 프로그램에 대 한 앱 등록 페이지로 이동 합니다. 
4. **API 사용 권한** 을 클릭 합니다. 1 단계에서 PowerShell 스크립트에 의해 부여 된 사용 권한이 표시 됩니다. 
5. 이 디렉터리의 모든 사용자에 게 액세스 권한을 부여하도록 **{tenantname}에 대한 관리자 동의 부여** 를 선택합니다. 

이제 API Management 인스턴스의 **그룹** 탭에서 외부 Azure AD 그룹을 추가할 수 있습니다.

1. 왼쪽 메뉴의 **개발자 포털** 에서 **그룹** 을 선택 합니다.
2. **AZURE AD 그룹 추가** 단추를 선택 합니다.

   !["A A D 그룹 추가" 단추](./media/api-management-howto-aad/api-management-with-aad008.png)
1. 드롭다운에서 **테 넌 트** 를 선택 합니다. 
2. 추가 하려는 그룹을 검색 하 고 선택 합니다.
3. **선택** 단추를 누릅니다.

외부 Azure AD 그룹을 추가한 후에는 해당 속성을 검토 하 고 구성할 수 있습니다. 
1. 그룹의 이름을 **그룹** 탭에서 선택합니다. 
2. 그룹에 대 한 **이름** 및 **설명** 정보를 편집 합니다.
 
구성 된 Azure AD 인스턴스의 사용자는 이제 다음을 수행할 수 있습니다.
* 개발자 포털에 로그인 합니다. 
* 표시 되는 그룹을 보고 구독 합니다.

> [!NOTE]
> [Microsoft ID 플랫폼 문서의 사용 권한 및 동의](../active-directory/develop/v2-permissions-and-consent.md#permission-types) 에서 **위임** 된 권한 및 **응용 프로그램** 권한 유형 간의 차이점에 대해 자세히 알아보세요.

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> 개발자 포털: Azure AD 계정 인증 추가

개발자 포털에서 **로그인 단추** 를 사용 하 여 Azure AD에 로그인 할 수 있습니다. OAuth 위젯은 기본 개발자 포털 콘텐츠의 로그인 페이지에 포함 되어 있습니다.

새 사용자가 Azure AD를 사용 하 여 로그인 할 때 새 계정이 자동으로 생성 되기는 하지만, 동일한 위젯을 등록 페이지에 추가 하는 것이 좋습니다. **등록 양식: OAuth** 위젯은 OAuth로 등록하는 데 사용되는 양식을 나타냅니다.

> [!IMPORTANT]
> Azure AD 변경 내용을 적용 하려면 포털을 다시 [게시](api-management-howto-developer-portal-customize.md#publish) 해야 합니다.

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>레거시 개발자 포털: Azure AD를 사용 하 여 로그인 하는 방법

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

이전 섹션에서 구성한 Azure AD 계정을 사용 하 여 개발자 포털에 로그인 하려면 다음을 수행 합니다.

1. Active Directory 응용 프로그램 구성의 로그인 URL을 사용 하 여 새 브라우저 창을 엽니다. 
2. **Azure Active Directory** 를 선택합니다.

   ![로그인 페이지][api-management-dev-portal-signin]

1. Azure AD에서 사용자 중 하나의 자격 증명을 입력 합니다.
2. **로그인** 을 선택합니다.

   ![사용자 이름 및 암호로 로그인][api-management-aad-signin]

1. 등록 양식이 표시 되 면 필요한 추가 정보를 모두 입력 합니다. 
2. **등록** 을 선택 합니다.

   ![등록 양식의 "등록" 단추][api-management-complete-registration]

이제 사용자는 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료 이후 개발자 포털][api-management-registration-complete]

## <a name="next-steps"></a>다음 단계

- [AZURE AD에서 OAuth 2.0 권한 부여를 사용 하 여 API Management에서 WEB API 백 엔드를 보호](./api-management-howto-protect-backend-with-aad.md) 하는 방법을 알아봅니다.
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
