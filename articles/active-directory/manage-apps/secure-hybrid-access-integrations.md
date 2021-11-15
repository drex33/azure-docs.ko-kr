---
title: Azure AD 파트너 통합을 사용하여 하이브리드 액세스 보호
description: 고객이 SaaS 애플리케이션을 검색하여 Azure AD로 마이그레이션하고, 레거시 인증 방법을 사용하는 앱을 Azure AD와 연결하는 데 도움이 됩니다.
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c8bffabb1cb2e12fb8144e66e038f500a8e5229
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253864"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>Azure Active Directory 파트너 통합을 사용하여 하이브리드 액세스 보호

Azure AD(Azure Active Directory)는 고도로 연결된 클라우드 기반 세계에서 애플리케이션을 안전하게 유지하는 데 도움이 되는 최신 인증 프로토콜을 지원합니다. 그러나 많은 비즈니스 애플리케이션이 보호된 회사 네트워크에서 작동하도록 만들어졌으며, 이러한 애플리케이션 중 일부는 레거시 인증 방법을 사용합니다. 회사에서 제로 트러스트 전략을 구축하고 하이브리드 및 클라우드 우선 작업 환경을 지원하려고 하므로 앱을 Azure AD에 연결하고 레거시 애플리케이션에 대한 최신 인증 솔루션을 제공하는 솔루션이 필요합니다.

Azure AD는 기본적으로 SAML, WS-Fed 및 OIDC와 같은 최신 프로토콜을 지원합니다. Azure AD의 앱 프록시는 Kerberos 및 헤더 기반 인증을 지원합니다. SSH, NTLM, LDAP 및 쿠키와 같은 다른 프로토콜은 아직 지원되지 않습니다. 그러나 ISV는 이러한 애플리케이션을 Azure AD와 연결하여 제로 트러스트에 대한 경험에서 고객을 지원하는 솔루션을 만들 수 있습니다.

ISV는 고객이 SaaS(Software as a Service) 애플리케이션을 검색하고 Azure AD로 마이그레이션하는 데 도움을 줄 수 있습니다. 또한 레거시 인증 방법을 사용하는 앱을 Azure AD와 연결할 수 있습니다. 이렇게 하면 고객이 단일 플랫폼(Azure AD)으로 통합하여 앱 관리를 간소화하고 제로 트러스트 원칙을 구현할 수 있습니다. 레거시 인증을 사용하는 앱을 지원하면 사용자의 보안이 높아집니다. 이 솔루션은 고객이 최신 인증 프로토콜을 지원하도록 앱을 현대화할 때까지 훌륭한 임시방편이 될 수 있습니다.

## <a name="solution-overview"></a>솔루션 개요

빌드하는 솔루션에 포함될 수 있는 부분은 다음과 같습니다.

1. **앱 검색**. 고객이 사용하는 모든 애플리케이션을 인식하지 못하는 경우가 있습니다. 따라서 첫 번째 단계로 애플리케이션 검색 기능을 솔루션에 빌드하고, 사용자 인터페이스에서 검색된 애플리케이션을 표시할 수 있습니다. 이렇게 하면 고객이 Azure AD와 애플리케이션을 통합할 때 사용할 방법의 우선 순위를 지정할 수 있습니다.
2. **앱 마이그레이션**. 다음으로, 고객이 Azure AD 포털로 이동하지 않고도 Azure AD와 앱을 직접 통합할 수 있는 제품 내 워크플로를 만들 수 있습니다. 검색 기능을 솔루션에 구현하지 않는 경우, 여기서 솔루션을 시작하여 고객이 알고 있는 애플리케이션을 Azure AD와 통합할 수 있습니다.
3. **레거시 인증 지원**. SSO(Single Sign-On) 및 기타 기능의 이점을 얻을 수 있도록 레거시 인증 방법을 사용하여 앱을 Azure AD에 연결할 수 있습니다.
4. **조건부 액세스**. 추가 기능으로, 고객이 Azure AD 포털로 이동하지 않고도 솔루션 내에서 Azure AD [조건부 액세스](../conditional-access/overview.md) 정책을 애플리케이션에 적용할 수 있습니다.

이 가이드의 나머지 부분에는 기술 고려 사항 및 솔루션 구현을 위한 권장 사항이 설명되어 있습니다.

## <a name="publishing-your-application-to-azure-marketplace"></a>Azure Marketplace에 애플리케이션 게시

[Azure Marketplace에 게시](../develop/v2-howto-app-gallery-listing.md)하는 프로세스에 따라 애플리케이션을 Azure AD와 미리 통합하여 SSO 및 자동화된 프로비전을 지원할 수 있습니다. Azure Marketplace는 IT 관리자를 위한 신뢰할 수 있는 애플리케이션 원본입니다. 여기에 나열된 애플리케이션은 Azure AD와 호환되는 것으로 확인되었습니다. SSO를 지원하고, 사용자 프로비저닝을 자동화하며, 자동화된 앱 등록을 통해 고객 테넌트에 쉽게 통합할 수 있습니다.

또한 고객이 앱의 신뢰할 수 있는 게시자임을 알 수 있도록 [확인된 게시자](../develop/publisher-verification-overview.md)가 되는 것이 좋습니다.

## <a name="enabling-single-sign-on-for-it-admins"></a>IT 관리자 Single Sign-On 사용

[OIDC 또는 SAML을 선택](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/)하여 IT 관리자 SSO를 솔루션에 사용할 수 있도록 설정합니다. 가장 좋은 옵션은 OIDC를 사용하는 것입니다. 

Microsoft Graph는 [OIDC/OAuth](../develop/v2-protocols-oidc.md)를 사용합니다. 솔루션에서 IT 관리자 SSO용 Azure AD와 함께 OIDC를 사용하는 경우 고객은 원활한 엔드투엔드 환경을 갖출 수 있습니다. OIDC를 사용하여 솔루션에 로그인하고, Azure AD에서 발급한 것과 동일한 JWT(JSON Web Token)를 사용하여 Microsoft Graph와 상호 작용할 수 있습니다.

솔루션에서 [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/)을 IT 관리자 SSO에 대신 사용하는 경우 SAML 토큰을 사용하면 솔루션에서 Microsoft Graph와 상호 작용할 수 없습니다. SAML은 IT 관리자 SSO에 계속 사용할 수 있지만, 솔루션에서 Azure AD로부터 JWT를 가져와서 Microsoft Graph와 제대로 상호 작용할 수 있도록 Azure AD와 OIDC의 통합을 지원해야 합니다. 다음 접근법 중 하나를 사용할 수 있습니다.

- **권장 SAML 방법**: Azure Marketplace에서 [OIDC 앱](../saas-apps/openidoauth-tutorial.md)인 새 등록을 만듭니다. 그러면 고객에게 가장 원활한 환경이 제공됩니다. SAML 및 OIDC 앱 모두를 테넌트에 추가합니다. 사용 중인 애플리케이션이 현재 Azure AD 갤러리에 없는 경우, 갤러리에 없는 [다중 테넌트 애플리케이션](../develop/howto-convert-app-to-be-multi-tenant.md)으로 시작할 수 있습니다.

- **대체 SAML 방법**: 고객이 Azure AD 테넌트에서 수동으로 [OIDC 애플리케이션 등록을 만들고](../saas-apps/openidoauth-tutorial.md), 이 문서의 뒷부분에 지정된 올바른 URI, 엔드포인트 및 권한을 설정해야 합니다.

[client_credentials 권한 부여 유형](../develop/v2-oauth2-client-creds-grant-flow.md#get-a-token)을 사용하는 것이 좋습니다. 각 고객이 솔루션을 통해 사용자 인터페이스에서 클라이언트 ID와 비밀을 입력할 수 있도록 하고 이 정보를 저장해야 합니다. Azure AD에서 JWT를 가져온 다음, 이를 사용하여 Microsoft Graph와 상호 작용합니다.

이 경로를 선택하는 경우 Azure AD 테넌트 내에서 이 애플리케이션 등록을 만드는 방법을 설명하는 고객용 기성 설명서가 있어야 합니다. 이 정보에는 엔드포인트, URI 및 필요한 권한이 ​​포함됩니다.

> [!NOTE]
> 애플리케이션을 IT 관리자 또는 사용자 SSO에 사용하려면 먼저 고객의 IT 관리자가 [테넌트의 애플리케이션에 동의](./grant-admin-consent.md)해야 합니다.

## <a name="authentication-flows"></a>인증 흐름

이 솔루션에는 다음 시나리오를 지원하는 세 가지 주요 인증 흐름이 포함됩니다.

- 고객의 IT 관리자가 SSO를 통해 로그인하여 솔루션을 관리합니다.

- 고객의 IT 관리자가 솔루션을 사용하여 Microsoft Graph를 통해 애플리케이션을 Azure AD와 통합합니다.

- 사용자가 솔루션 및 Azure AD로 보호되는 레거시 애플리케이션에 로그인합니다.

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>고객의 IT 관리자가 솔루션에 대한 Single Sign-On 수행

고객의 IT 관리자가 로그인할 때 솔루션에서 SAML 또는 OIDC를 SSO에 사용할 수 있습니다. 어떤 방법이든 IT 관리자가 Azure AD 자격 증명을 사용하여 솔루션에 로그인할 수 있는 것이 좋습니다. 원활한 환경을 가능하게 하고 이미 있는 기존 보안 제어를 사용할 수 있습니다. 솔루션은 SAML 또는 OIDC를 통해 SSO용 Azure AD와 통합되어야 합니다.

이 사용자 인증 흐름의 다이어그램과 요약은 다음과 같습니다.

![사용자 인증 흐름에서 로그인하기 위해 솔루션에서 Azure AD로 리디렉션된 다음, Azure AD에서 솔루션으로 다시 리디렉션되는 IT 관리자를 보여 주는 다이어그램](./media/secure-hybrid-access-integrations/admin-flow.png)

1. IT 관리자가 Azure AD 자격 증명을 사용하여 솔루션에 로그인하려고 합니다.

2. 솔루션에서 SAML 또는 OIDC 로그인 요청을 사용하여 IT 관리자를 Azure AD로 리디렉션합니다.

3. Azure AD에서 IT 관리자를 인증한 다음, 솔루션 내에서 권한을 부여받기 위해 SAML 토큰 또는 JWT를 사용하여 솔루션으로 다시 보냅니다.

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>IT 관리자가 솔루션을 사용하여 Azure AD와 애플리케이션 통합

IT 관리자 여정의 두 번째 방법은 솔루션을 사용하여 애플리케이션을 Azure AD와 통합하는 것입니다. 이를 위해 솔루션은 Microsoft Graph를 사용하여 애플리케이션 등록 및 Azure AD 조건부 액세스 정책을 만듭니다.

이 사용자 인증 흐름의 다이어그램과 요약은 다음과 같습니다.

![사용자 인증 흐름에서 IT 관리자, Azure Active Directory, 솔루션 및 Microsoft Graph 간의 리디렉션 및 기타 상호 작용에 대한 다이어그램](./media/secure-hybrid-access-integrations/registration-flow.png)


1. IT 관리자가 Azure AD 자격 증명을 사용하여 솔루션에 로그인하려고 합니다.

2. 솔루션에서 SAML 또는 OIDC 로그인 요청을 사용하여 IT 관리자를 Azure AD로 리디렉션합니다.

3. Azure AD에서 IT 관리자를 인증한 다음, 솔루션 내에서 권한을 부여하기 위해 SAML 토큰 또는 JWT를 사용하여 솔루션으로 다시 보냅니다.

4. IT 관리자가 Azure AD 포털로 이동하지 않고 애플리케이션 중 하나를 Azure AD와 통합하려는 경우 솔루션에서 기존 JWT를 통해 Microsoft Graph를 호출하여 해당 애플리케이션을 등록하거나 Azure AD 조건부 액세스 정책을 적용합니다.

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>사용자가 솔루션 및 Azure AD로 보호되는 애플리케이션에 로그인

사용자가 솔루션 및 Azure AD로 보호되는 개별 애플리케이션에 로그인해야 하는 경우 OIDC 또는 SAML을 사용합니다. 애플리케이션에서 Microsoft Graph 또는 Azure AD로 보호되는 API와 상호 작용해야 하는 경우 OICD를 사용하도록 구성하는 것이 좋습니다. 이 구성을 통해 애플리케이션에 인증하기 위해 애플리케이션에서 Azure AD로부터 가져오는 JWT를 Microsoft Graph와 상호 작용하는 데 적용할 수도 있습니다. 개별 애플리케이션에서 Microsoft Graph 또는 Azure AD로 보호되는 API와 상호 작용할 필요가 없는 경우 SAML만으로 충분합니다.

이 사용자 인증 흐름의 다이어그램과 요약은 다음과 같습니다.

![사용자 인증 흐름에서 사용자, Azure Active Directory, 솔루션 및 애플리케이션 간의 리디렉션 및 기타 상호 작용에 대한 다이어그램](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. 사용자가 솔루션 및 Azure AD로 보호되는 애플리케이션에 로그인하려고 합니다.
2. 솔루션에서 SAML 또는 OIDC 로그인 요청을 사용하여 사용자를 Azure AD로 리디렉션합니다.
3. Azure AD에서 사용자를 인증한 다음, 솔루션 내에서 권한을 부여하기 위해 SAML 토큰 또는 JWT를 사용하여 솔루션으로 다시 보냅니다.
4. 권한이 부여되면 솔루션에서 애플리케이션의 기본 설정 프로토콜을 사용하여 애플리케이션에 대한 원래 요청을 수행하도록 허용합니다.

## <a name="summary-of-microsoft-graph-apis"></a>Microsoft Graph API 요약

솔루션에서 사용해야 하는 API는 다음과 같습니다. Azure AD를 사용하면 위임된 권한 또는 애플리케이션 권한을 구성할 수 있습니다. 이 솔루션에서는 위임된 권한만 필요합니다.

- [애플리케이션 템플릿 API](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/): Azure Marketplace를 검색하려는 경우 이 API를 사용하여 일치하는 애플리케이션 템플릿을 찾을 수 있습니다. **필요한 권한**: Application.Read.All

- [애플리케이션 등록 API](/graph/api/application-post-applications): 사용자가 고객이 솔루션으로 보호한 애플리케이션에 로그인할 수 있도록 이 API를 사용하여 OIDC 또는 SAML 애플리케이션 등록을 만듭니다. 이렇게 하면 이러한 애플리케이션도 Azure AD로 보호할 수 있습니다. **필요한 권한**: Application.Read.All, Application.ReadWrite.All

- [서비스 주체 API](/graph/api/serviceprincipal-update): 앱이 등록되면 일부 SSO 속성을 설정하도록 서비스 주체 개체를 업데이트해야 합니다. **필요한 권한**: Application.ReadWrite.All, Directory.AccessAsUser.All, AppRoleAssignment.ReadWrite.All(할당용)

- [조건부 액세스 API](/graph/api/resources/conditionalaccesspolicy): 이러한 사용자 애플리케이션에도 Azure AD 조건부 액세스 정책을 적용하려는 경우 이 API를 사용할 수 있습니다. **필요한 권한**: Policy.Read.All, Policy.ReadWrite.ConditionalAccess 및 Application.Read.All

## <a name="example-graph-api-scenarios"></a>예제 Graph API 시나리오

이 섹션에서는 Microsoft Graph API를 사용하여 애플리케이션 등록을 구현하고, 레거시 애플리케이션을 연결하고, 솔루션을 통해 조건부 액세스 정책을 사용하도록 설정하는 참조 예제를 제공합니다. 관리자 동의를 자동화하고, 토큰 서명 인증서를 가져오고, 사용자 및 그룹을 할당하는 방법에 대한 지침도 제공합니다. 이 기능은 솔루션에 유용할 수 있습니다.

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>Graph API를 사용하여 Azure AD에 앱 등록

#### <a name="add-apps-that-are-in-azure-marketplace"></a>Azure Marketplace에 있는 앱 추가

고객이 사용하는 애플리케이션 중 일부는 이미 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)에서 사용할 수 있습니다. 이러한 애플리케이션을 프로그래밍 방식으로 고객의 테넌트에 추가하는 솔루션을 만들 수 있습니다. 다음 코드는 Microsoft Graph API를 사용하여 Azure Marketplace에서 일치하는 템플릿을 검색한 다음, 애플리케이션을 고객의 Azure AD 테넌트에 등록하는 예제입니다.

Azure Marketplace에서 일치하는 애플리케이션을 검색합니다. 애플리케이션 템플릿 API를 사용하는 경우 표시 이름은 대/소문자를 구분합니다.

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

이전 API 호출에서 일치하는 항목이 발견되면 ID를 캡처한 다음, JSON 본문에서 사용자에게 친숙한 애플리케이션에 대한 표시 이름을 제공하면서 다음 API를 호출합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

위의 API 호출을 수행하면 서비스 주체 개체도 생성되며 이 경우 몇 초 정도 걸릴 수 있습니다. 애플리케이션 ID와 서비스 주체 ID를 캡처해야 합니다. 이는 다음 API 호출에서 사용합니다.

다음으로, SAML 프로토콜과 적절한 로그인 URL을 사용하여 서비스 주체 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

마지막으로, 적절한 리디렉션 URI 및 식별자 URI를 사용하여 애플리케이션 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>Azure Marketplace에 없는 앱 추가

Azure Marketplace에서 일치하는 항목을 찾을 수 없거나 사용자 지정 애플리케이션을 통합하려는 경우 **8adf8e6e-67b2-4cf2-a259-e3dc5476c621** 템플릿 ID를 사용하여 Azure AD에 사용자 지정 애플리케이션을 등록할 수 있습니다. 그런 다음, JSON 본문에서 사용자에게 친숙한 애플리케이션에 대한 표시 이름을 제공하면서 다음 API를 호출합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

위의 API 호출을 수행하면 서비스 주체 개체도 생성되며 이 경우 몇 초 정도 걸릴 수 있습니다. 애플리케이션 ID와 서비스 주체 ID를 캡처해야 합니다. 이는 다음 API 호출에서 사용합니다.

다음으로, SAML 프로토콜과 적절한 로그인 URL을 사용하여 서비스 주체 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

마지막으로, 적절한 리디렉션 URI 및 식별자 URI를 사용하여 애플리케이션 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>Azure AD Single Sign-On으로 전환

SaaS 애플리케이션이 Azure AD 내에 등록된 후에도 Azure AD를 ID 공급자로 사용하기 시작하도록 애플리케이션을 전환해야 합니다. 이때 다음과 같은 두 가지 방법을 사용할 수 있습니다.

- 애플리케이션에서 한 번 클릭 SSO를 지원하는 경우 Azure AD에서 고객을 위해 애플리케이션을 전환할 수 있습니다. 고객은 Azure AD 포털로 이동하여 지원되는 SaaS 애플리케이션에 대한 관리 자격 증명을 사용하여 한 번 클릭 SSO를 수행하기만 하면 됩니다. 자세한 내용은 [Single Sign-On의 한 번 클릭 앱 구성](./one-click-sso-tutorial.md)을 참조하세요.
- 애플리케이션에서 한 번 클릭 SSO를 지원하지 않는 경우 고객은 Azure AD 사용을 시작하도록 애플리케이션을 수동으로 전환해야 합니다. 자세한 내용은 [SaaS 애플리케이션과 Azure Active Directory를 통합하는 방법에 대한 자습서](../saas-apps/tutorial-list.md)를 참조하세요.

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>레거시 인증 방법을 사용하여 Azure AD에 앱 연결

여기서는 솔루션이 Azure AD와 애플리케이션 사이에 위치하여 지원되지 않는 애플리케이션의 경우에도 고객이 SSO 및 기타 Azure Active Directory 기능의 이점을 얻을 수 있습니다. 이를 위해 사용자가 레거시 프로토콜을 사용하여 애플리케이션에 액세스할 수 있기 전에 이러한 애플리케이션에서 Azure AD를 호출하여 사용자를 인증하고 Azure AD 조건부 액세스 정책을 적용할 수 있습니다.

고객이 콘솔에서 직접 이 통합을 수행하여 검색 및 통합이 원활한 엔드투엔드 환경이 되도록 할 수 있습니다. 여기에는 플랫폼과 Azure AD 간에 SAML 또는 OIDC 애플리케이션 등록을 만드는 플랫폼이 포함됩니다.

#### <a name="create-a-saml-application-registration"></a>SAML 애플리케이션 등록 만들기

SAML 애플리케이션 등록을 만들려면 **8adf8e6e-67b2-4cf2-a259-e3dc5476c621** 이라는 사용자 지정 애플리케이션 템플릿 ID를 사용자 지정 애플리케이션에 사용합니다. 그런 다음, JSON 본문에서 사용자에게 친숙한 표시 이름을 제공하면서 다음 API를 호출합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

위의 API 호출을 수행하면 서비스 주체 개체도 생성되며 이 경우 몇 초 정도 걸릴 수 있습니다. 애플리케이션 ID와 서비스 주체 ID를 캡처해야 합니다. 이는 다음 API 호출에서 사용합니다.

다음으로, SAML 프로토콜과 적절한 로그인 URL을 사용하여 서비스 주체 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

마지막으로, 적절한 리디렉션 URI 및 식별자 URI를 사용하여 애플리케이션 개체를 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>OIDC 애플리케이션 등록 만들기

OIDC 애플리케이션 등록을 만들려면 **8adf8e6e-67b2-4cf2-a259-e3dc5476c621** 이라는 애플리케이션 템플릿 ID를 사용자 지정 애플리케이션에 사용합니다. 그런 다음, JSON 본문에서 사용자에게 친숙한 표시 이름을 제공하면서 다음 API를 호출합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

API 호출에서 애플리케이션 ID와 서비스 주체 ID를 캡처합니다. 이는 다음 API 호출에서 사용합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> `resourceAccess` 노드 내에서 나열된 API 권한은 사용자가 솔루션에 로그인하는 데 충분한 *openid*, *User.Read* 및 *offline_access* 권한을 애플리케이션에 부여합니다. 권한에 대한 자세한 내용은 [Microsoft Graph 권한 참조](/graph/permissions-reference/)를 참조하세요.

### <a name="apply-conditional-access-policies"></a>조건부 액세스 정책 적용

고객과 파트너는 Microsoft Graph API를 사용하여 조건부 액세스 정책을 만들거나 고객 애플리케이션에 적용할 수도 있습니다. 파트너의 경우 이렇게 하면 고객이 Azure AD 포털로 이동하지 않고도 솔루션에서 직접 이러한 정책을 적용할 수 있으므로 추가 가치를 제공할 수 있습니다. 

Azure AD 조건부 액세스 정책을 적용하는 경우 두 가지 옵션이 있습니다.

- 애플리케이션을 기존 조건부 액세스 정책에 할당합니다.
- 새 조건부 액세스 정책을 만들고, 애플리케이션을 해당 새 정책에 할당합니다.

#### <a name="use-an-existing-conditional-access-policy"></a>기존 조건부 액세스 정책 사용

먼저, 다음 쿼리를 실행하여 모든 조건부 액세스 정책의 목록을 가져옵니다. 수정하려는 정책의 개체 ID를 가져옵니다.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

그런 다음, JSON 본문 내에서 `includeApplications` 범위에 있도록 애플리케이션 개체 ID를 포함하여 정책을 패치합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>새 조건부 액세스 정책 만들기

JSON 본문 내에서 `includeApplications` 범위에 있도록 애플리케이션 개체 ID를 추가합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

새 Azure AD 조건부 액세스 정책을 만들려는 경우 [조건부 액세스 API](../conditional-access/howto-conditional-access-apis.md) 사용을 시작하는 데 도움이 되는 몇 가지 추가 템플릿이 있습니다.

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>관리자 동의 자동화

고객이 수많은 애플리케이션을 플랫폼에서 Azure AD로 온보딩하는 경우 수동으로 많은 애플리케이션에 동의할 필요가 없도록 관리자 동의를 자동화할 수 있습니다. 이 자동화는 Microsoft Graph를 통해 수행할 수도 있습니다. 이전 API 호출에서 만든 애플리케이션의 서비스 주체 개체 ID와 고객 테넌트의 Microsoft Graph 서비스 주체 개체 ID가 모두 필요합니다.

다음 API를 호출하여 Microsoft Graph의 서비스 주체 개체 ID를 가져옵니다.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

관리자 동의를 자동화할 준비가 되면 다음 API를 호출합니다.

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>토큰 서명 인증서 가져오기

이러한 모든 애플리케이션에 대한 토큰 서명 인증서의 공개 부분을 가져오려면 해당 애플리케이션에 대한 Azure AD 메타데이터 엔드포인트에서 `GET`을 사용합니다.

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>사용자 및 그룹 할당

애플리케이션이 Azure AD에 게시되면 필요에 따라 사용자 및 그룹에 할당하여 [MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/) 포털에 표시되도록 할 수 있습니다. 이 할당은 애플리케이션을 만들 때 생성된 서비스 주체 개체에 저장됩니다.

먼저, 애플리케이션에서 연결했을 수 있는 모든 `AppRole` 인스턴스를 가져옵니다. 일반적으로 SaaS 애플리케이션에는 다양한 `AppRole` 인스턴스가 연결되어 있습니다. 사용자 지정 애플리케이션의 경우 일반적으로 하나의 기본 `AppRole` 인스턴스만 있습니다. 할당하려는 `AppRole` 인스턴스의 ID를 가져옵니다.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

다음으로, 애플리케이션에 할당하려는 Azure AD에서 사용자 또는 그룹의 개체 ID를 가져옵니다. 또한 이전 API 호출의 앱 역할 ID를 가져와서 서비스 주체에 대한 패치 본문의 일부로 제출합니다.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>파트너십

Microsoft는 기존 네트워킹 및 전송 컨트롤러를 사용하면서 레거시 애플리케이션을 보호할 수 있도록 다음 ADC(애플리케이션 전송 컨트롤러) 공급자와 협력하고 있습니다.

| **ADC 공급자** | **링크** |
| --- | --- |
| Akamai 엔터프라이즈 애플리케이션 액세스 | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](../saas-apps/akamai-tutorial.md) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](../saas-apps/citrix-netscaler-tutorial.md) |
| F5 Big-IP 액세스 정책 관리자 | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](./f5-aad-integration.md) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](../saas-apps/kemp-tutorial.md) |
| Pulse Secure Virtual Traffic Manager | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) |

다음 VPN 솔루션 공급자는 Azure AD와 연결하여 SSO 및 다단계 인증과 같은 최신 인증 및 권한 부여 방법을 사용하도록 설정합니다.

| **VPN 공급업체** | **링크** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](../saas-apps/cisco-anyconnect.md) |
| Fortinet FortiGate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](../saas-apps/fortigate-ssl-vpn-tutorial.md) |
| F5 Big-IP 액세스 정책 관리자 | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](./f5-aad-password-less-vpn.md) |
| Palo Alto Networks GlobalProtect | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](../saas-apps/paloaltoadmin-tutorial.md) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](../saas-apps/pulse-secure-pcs-tutorial.md) |

다음 SDP(소프트웨어 정의 경계) 솔루션 공급자는 Azure AD와 연결하여 SSO 및 다단계 인증과 같은 최신 인증 및 권한 부여 방법을 사용하도록 설정합니다.

| **SDP 공급업체** | **링크** |
| --- | --- |
| Datawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](./datawiza-with-azure-ad.md) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](../saas-apps/perimeter-81-tutorial.md) |
| Silverfort 인증 플랫폼 | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](./silverfort-azure-ad-integration.md) |
| Strata Maverics Identity Orchestrator | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](../saas-apps/zscalerprivateaccess-tutorial.md) |
