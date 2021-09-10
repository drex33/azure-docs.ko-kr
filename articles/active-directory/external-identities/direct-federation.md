---
title: B2B용 SAML/WS-Fed IdP(ID 공급자)와의 페더레이션 - Azure AD
description: 게스트가 Azure AD 앱에 로그인할 수 있도록 SAML 또는 WS-Fed ID 공급자와 직접 페더레이션합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: da0d7c9812575ce6b957e70dbe4f8bc165f39315
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113138732"
---
# <a name="federation-with-samlws-fed-identity-providers-for-guest-users-preview"></a>게스트 사용자를 위한 SAML/WS-Fed ID 공급자와의 페더레이션(미리 보기)

> [!NOTE]
>- Azure Active Directory의 ‘직접 페더레이션’은 이제 ‘SAML/WS-Fed IdP(ID 공급자) 페더레이션’이라고 합니다. 
>- SAML/WS-Fed IdP 페더레이션은 Azure Active Directory의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IdP(ID 공급자)가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 모든 조직과의 페더레이션을 설정하는 방법을 설명합니다. 파트너 IdP와의 페더레이션을 설정하면 해당 도메인의 새 게스트 사용자가 자신의 IdP 관리형 조직 계정을 사용하여 Azure AD 테넌트에 로그인하고 협업을 시작할 수 있습니다. 게스트 사용자는 별도의 Azure AD 계정을 만들 필요가 없습니다.

> [!IMPORTANT]
> - 인증 URL 도메인이 대상 도메인과 일치하거나 허용 IdP에서 제공한 것이어야 한다는 제한이 없어졌습니다. 자세한 내용은 [1단계: 파트너가 DNS 텍스트 레코드를 업데이트해야 하는지 확인](#step-1-determine-if-the-partner-needs-to-update-their-dns-text-records)을 참조하세요.
>-  이제 파트너는 SAML 또는 WS-Fed 기반 IdP의 대상을 테넌트 대상으로 설정하는 것이 좋습니다. 아래의 [SAML 2.0](#required-saml-20-attributes-and-claims) 및 [WS-Fed](#required-ws-fed-attributes-and-claims) 필수 특성 및 클레임 섹션을 참조하세요.

## <a name="when-is-a-guest-user-authenticated-with-samlws-fed-idp-federation"></a>게스트 사용자가 SAML/WS-Fed IdP 페더레이션을 통해 인증되는 경우는 언제인가요?

조직 SAML/WS-Fed IdP와의 페더레이션을 설정하면 초대하는 모든 새 게스트 사용자가 해당 SAML/WS-Fed IdP를 사용하여 인증됩니다. 페더레이션을 설정해도 이미 초대에 응한 게스트 사용자에 대한 인증 방법은 변경되지 않습니다. 다음은 몇 가지 예입니다.

 - 게스트 사용자가 이미 초대에 응한 후에 조직 SAML/WS-Fed IdP와의 페더레이션이 설정되는 경우 해당 게스트 사용자는 페더레이션이 설정되기 전에 사용한 것과 동일한 인증 방법을 계속 사용할 수 있습니다.
 - 조직 SAML/WS-Fed IdP와의 페더레이션을 설정하고, 게스트 사용자를 초대한 다음, 나중에 파트너 조직이 Azure AD로 이동하는 경우 테넌트의 페더레이션 정책이 존재하는 한 이미 초대에 응한 게스트 사용자는 페더레이션된 SAML/WS-Fed IdP를 계속 사용할 수 있습니다.
 - 조직 SAML/WS-Fed IdP와의 페더레이션을 삭제하면 기존에 SAML/WS-Fed IdP를 사용하는 게스트 사용자는 로그인할 수 없습니다.

이러한 시나리오에서는 [해당 상환 상태를 재설정](reset-redemption-status.md)하여 게스트 사용자의 인증 방법을 업데이트할 수 있습니다.

SAML/WS-Fed IdP 페더레이션은 contoso.com 및 fabrikam.com과 같은 도메인 네임스페이스에 연결됩니다. AD FS 또는 타사 IdP와의 페더레이션을 설정하는 경우 조직은 하나 이상의 도메인 네임스페이스를 해당 IdP에 연결합니다.

## <a name="end-user-experience"></a>최종 사용자 환경 

SAML/WS-Fed IdP 페더레이션을 사용하는 경우 게스트 사용자는 자신의 조직 계정을 사용하여 Azure AD 테넌트에 로그인합니다. 사용자가 공유 리소스에 액세스하고 로그인하라는 메시지가 표시되면 사용자가 IdP로 리디렉션됩니다. 로그인에 성공하면 사용자는 Azure AD로 돌아가 리소스에 액세스할 수 있습니다. 사용자의 새로 고침 토큰은 12시간 동안 유효하며, 이는 Azure AD에서 [통과 새로 고침 토큰의 기본 길이](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties)입니다. 페더레이션된 IdP에서 SSO를 사용하도록 설정되면 사용자가 SSO를 수행하게 되며 초기 인증 후 로그인 프롬프트가 표시되지 않습니다.

## <a name="sign-in-endpoints"></a>로그인 엔드포인트

SAML/WS-Fed IdP 페더레이션 게스트 사용자는 이제 [공통 엔드포인트](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)(즉, 테넌트 컨텍스트를 포함하지 않는 일반 앱 URL)를 사용하여 다중 테넌트 또는 Microsoft 자사 앱에 로그인할 수 있습니다. 로그인 프로세스 중에 게스트 사용자는 **로그인 옵션** 을 선택한 다음, **조직에 로그인** 을 선택합니다. 그런 다음, 사용자는 조직의 이름을 입력하고 자체 자격 증명을 사용하여 계속 로그인합니다.

SAML/WS-Fed IdP 페더레이션 게스트 사용자는 테넌트 정보가 포함된 애플리케이션 엔드포인트를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

테넌트 정보(예: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`)를 포함하여 게스트 사용자에게 애플리케이션 또는 리소스에 대한 직접 링크를 제공할 수도 있습니다.

## <a name="limitations"></a>제한 사항

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD의 DNS 확인 도메인
관리되지 않는(이메일 확인 또는 "바이럴") Azure AD 테넌트를 포함하여 Azure AD에서 DNS로 확인되지 않은 도메인과 SAML/WS-Fed IdP 페더레이션을 설정할 수 있습니다. 그러나 기본 Azure AD 관리형 도메인 기능을 위해 Azure AD 확인 도메인에 대한 SAML/WS-Fed IdP 페더레이션을 차단합니다. Azure AD에서 DNS로 확인된 도메인을 사용하여 SAML/WS-Fed IdP 페더레이션을 설정하려고 하면 Azure Portal 또는 PowerShell에 오류가 표시됩니다.

### <a name="signing-certificate-renewal"></a>서명 인증서 갱신
IdP 설정에서 메타데이터 URL을 지정하는 경우 Azure AD에서 만료되는 서명 인증서를 자동으로 갱신합니다. 그러나 어떤 이유로든 인증서를 만료 시간 전에 회전하거나 메타데이터 URL을 제공하지 않으면 Azure AD에서 인증서를 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트해야 합니다.

### <a name="limit-on-federation-relationships"></a>페더레이션 관계에 대한 제한
현재 최대 1,000개의 페더레이션 관계가 지원됩니다. 이 제한에는 [내부 페더레이션](/powershell/module/msonline/set-msoldomainfederationsettings) 및 SAML/WS-Fed IdP 페더레이션이 모두 포함됩니다.

### <a name="limit-on-multiple-domains"></a>여러 도메인에 대한 제한
현재 동일한 테넌트의 여러 도메인과의 SAML/WS-Fed IdP 페더레이션을 지원하지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="can-i-set-up-samlws-fed-idp-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>비관리형(메일 확인) 테넌트가 있는 도메인과의 SAML/WS-Fed IdP 페더레이션을 설정할 수 있나요? 
예. 도메인이 확인되지 않고 테넌트에서 [관리자 인수](../enterprise-users/domains-admin-takeover.md)를 수행하지 않은 경우 해당 도메인과의 페더레이션을 설정할 수 있습니다. 사용자가 B2B 초대에 응하거나 현재 존재하지 않는 도메인을 사용하여 Azure AD에 대한 셀프 서비스 가입을 수행하는 경우 비관리형 또는 이메일 확인 테넌트가 만들어집니다. 이러한 도메인을 통해 SAML/WS-Fed IdP 페더레이션을 설정할 수 있습니다.
### <a name="if-samlws-fed-idp-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>SAML/WS-Fed IdP 페더레이션과 일회용 메일 암호 인증을 모두 사용하도록 설정하는 경우 우선으로 적용되는 방법은 무엇인가요?
파트너 조직과의 SAML/WS-Fed IdP 페더레이션을 설정하는 경우 이 설정은 해당 조직의 새 게스트 사용자에 대한 일회용 메일 암호 인증보다 우선으로 적용됩니다. 게스트 사용자가 SAML/WS-Fed IdP 페더레이션을 설정하기 전에 일회용 암호 인증을 사용하여 초대에 응한 경우 일회용 암호 인증을 계속 사용합니다.
### <a name="does-samlws-fed-idp-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>SAML/WS-Fed IdP 페더레이션에서 부분적으로 동기화된 테넌트로 인한 로그인 문제를 해결하나요?
아니요, 이 시나리오에서는 [일회용 이메일 암호](one-time-passcode.md) 기능을 사용해야 합니다. "부분적으로 동기화된 테넌트"는 온-프레미스 사용자 ID가 클라우드에 완전히 동기화되지 않은 파트너 Azure AD 테넌트를 나타냅니다. ID가 아직 클라우드에 없지만 B2B 초대에 응하려는 게스트는 로그인할 수 없습니다. 일회용 암호 기능을 사용하면 이 게스트가 로그인할 수 있습니다. SAML/WS-Fed IdP 페더레이션 기능은 게스트에 자신의 IdP 관리형 조직 계정이 있지만 조직에 Azure AD가 전혀 없는 시나리오를 해결합니다.
### <a name="once-samlws-fed-idp-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>조직에서 SAML/WS-Fed IdP 페더레이션을 구성하면 각 게스트를 보내고 개별 초대를 상환해야 하나요?
SAML/WS-Fed IdP 페더레이션을 설정해도 이미 초대를 받은 게스트 사용자에 대한 인증 방법은 변경되지 않습니다. [해당 상환 상태를 재설정](reset-redemption-status.md)하여 게스트 사용자의 인증 방법을 업데이트할 수 있습니다.

## <a name="step-1-determine-if-the-partner-needs-to-update-their-dns-text-records"></a>1단계: 파트너가 DNS 텍스트 레코드를 업데이트해야 하는지 확인

파트너의 IdP에 따라 파트너는 여러분과의 페더레이션을 사용하기 위해 DNS 레코드를 업데이트해야 할 수 있습니다. DNS 업데이트가 필요한지 확인하려면 다음 단계를 사용합니다.

1. 파트너의 IdP가 다음 허용 IdP 중 하나인 경우에는 DNS 변경이 필요하지 않습니다(이 목록은 변경될 수 있음).

     - accounts.google.com
     - pingidentity.com
     - login.pingone.com
     - okta.com
     - oktapreview.com
     - okta-emea.com
     - my.salesforce.com
     - federation.exostar.com
     - federation.exostartest.com
     - idaptive.app
     - idaptive.qa

2. IdP가 이전 단계에 나열된 허용 공급자 중 하나가 아닌 경우 파트너의 IdP 인증 URL을 확인하여 도메인이 대상 도메인 또는 대상 도메인 내의 호스트와 일치하는지 확인합니다. 즉, `fabrikam.com`에 대한 페더레이션을 설정하는 경우:

     - 인증 URL이 `https://fabrikam.com` 또는 `https://sts.fabrikam.com/adfs`(동일한 도메인의 호스트)인 경우 DNS를 변경할 필요가 없습니다.
     - 인증 URL이 `https://fabrikamconglomerate.com/adfs`  또는  `https://fabrikam.com.uk/adfs`인 경우 도메인이 fabrikam.com 도메인과 일치하지 않으므로 파트너가 DNS 구성에서 인증 URL에 대한 텍스트 레코드를 추가해야 합니다. 이에 해당하는 경우에는 다음 단계로 이동합니다.

3. 이전 단계에 따라 DNS 변경이 필요한 경우 다음 예제와 같이 파트너에게 해당 도메인의 DNS 레코드에 TXT 레코드를 추가할 것을 요청합니다.

   `fabrikam.com.  IN   TXT   DirectFedAuthUrl=https://fabrikamconglomerate.com/adfs`

## <a name="step-2-configure-the-partner-organizations-idp"></a>2단계: 파트너 조직의 IdP 구성

다음으로, 파트너 조직에서 필요한 클레임 및 신뢰 당사자 트러스트를 통해 IdP를 구성해야 합니다.

> [!NOTE]
> 페더레이션을 위해 SAML/WS-Fed IdP를 구성하는 방법을 설명하기 위해 AD FS(Active Directory Federation Services)를 예로 사용합니다. 페더레이션을 준비하기 위해 AD FS를 SAML 2.0 또는 WS-Fed IdP로 구성하는 방법에 대한 예제를 제공하는 [AD FS를 사용하여 SAML/WS-Fed IdP 페더레이션 구성](direct-federation-adfs.md) 문서를 참조하세요.

### <a name="saml-20-configuration"></a>SAML 2.0 구성

Azure AD B2B는 아래에 나열된 특정 요구 사항에 따라 SAML 프로토콜을 사용하는 IdP와 페더레이션하도록 구성할 수 있습니다. SAML IdP와 Azure AD 간의 트러스트를 설정하는 방법에 대한 자세한 내용은 [Single Sign-On에 SAML 2.0 IdP(ID 공급자) 사용](../hybrid/how-to-connect-fed-saml-idp.md)을 참조하세요.  

> [!NOTE]
> SAML/WS-Fed IdP 페더레이션의 대상 도메인은 Azure AD에서 DNS로 확인되지 않아야 합니다. 자세한 내용은[제한 사항](#limitations) 섹션을 참조하세요.

#### <a name="required-saml-20-attributes-and-claims"></a>필요한 SAML 2.0 특성 및 클레임
다음 표에는 타사 IdP에서 구성해야 하는 특정 특성 및 클레임에 대한 요구 사항이 나와 있습니다. 페더레이션을 설정하려면 IdP의 SAML 2.0 응답에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다.

IdP의 SAML 2.0 응답에 필요한 특성은 다음과 같습니다.

|attribute  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`https://login.microsoftonline.com/<tenant ID>/`(권장 테넌트 대상) `<tenant ID>`를 페더레이션을 설정할 Azure AD 테넌트의 테넌트 ID로 바꿉니다.<br><br>`urn:federation:MicrosoftOnline`(이 값은 사용되지 않을 예정입니다.)          |
|발급자     |파트너 IdP의 발급자 URI입니다(예: `http://www.example.com/exk10l6w90DHM0yi...`).         |


IdP에서 발급한 SAML 2.0 토큰에 필요한 클레임은 다음과 같습니다.

|attribute  |값  |
|---------|---------|
|NameID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 구성

Azure AD B2B는 아래에 나열된 몇 가지 특정 요구 사항에 따라 WS-Fed 프로토콜을 사용하는 IdP와 페더레이션하도록 구성할 수 있습니다. Azure AD와의 호환성에 대해 현재 두 개의 WS-Fed 공급자(AD FS 및 Shibboleth)가 테스트되었습니다. Azure AD를 사용하여 WS-Fed 규격 공급자 간에 신뢰 당사자 트러스트를 설정하는 방법에 대한 자세한 내용은 [Azure AD 공급자 호환성 문서](https://www.microsoft.com/download/details.aspx?id=56843)에서 제공하는 "WS 프로토콜을 사용하는 STS 통합 백서"를 참조하세요.

> [!NOTE]
> 페더레이션의 대상 도메인에 대한 DNS는 Azure AD에서 확인되지 않아야 합니다. 자세한 내용은 [제한 사항](#limitations) 섹션을 참조하세요.

#### <a name="required-ws-fed-attributes-and-claims"></a>필요한 WS-Fed 특성 및 클레임

다음 표에는 타사 WS-Fed IdP에서 구성해야 하는 특정 특성 및 클레임에 대한 요구 사항이 나와 있습니다. 페더레이션을 설정하려면 IdP의 WS-Fed 메시지에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다.

IdP의 WS-Fed 메시지에 필요한 특성은 다음과 같습니다.
 
|attribute  |값  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`https://login.microsoftonline.com/<tenant ID>/`(권장 테넌트 대상) `<tenant ID>`를 페더레이션할 Azure AD 테넌트의 테넌트 ID로 바꿉니다.<br><br>`urn:federation:MicrosoftOnline`(이 값은 사용되지 않을 예정입니다.)          |
|발급자     |파트너 IdP의 발급자 URI입니다(예: `http://www.example.com/exk10l6w90DHM0yi...`).         |

IdP에서 발급한 WS-Fed 토큰에 필요한 클레임은 다음과 같습니다.

|attribute  |값  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-3-configure-samlws-fed-idp-federation-in-azure-ad"></a>3단계: Azure AD에서 SAML/WS-Fed IdP 페더레이션 구성

다음으로, Azure AD의 1단계에서 구성된 IdP를 사용하여 페더레이션을 구성합니다. Azure AD 포털 또는 PowerShell을 사용할 수 있습니다. 페더레이션 정책이 적용될 때까지 5-10분 정도 걸릴 수 있습니다. 이 기간 동안에는 페더레이션 도메인에 대한 초대에 응하려고 시도하지 마세요. 다음과 같은 특성이 필요합니다.

- 파트너 IdP의 발급자 URI
- 파트너 IdP의 수동 인증 엔드포인트(https만 지원됨)
- 인증서

### <a name="to-configure-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 페더레이션을 구성하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** > **모든 ID 공급자** 를 차례로 선택합니다.
3. **새 SAML/WS-Fed IdP** 를 선택합니다.

    ![새 SAML 또는 WS-Fed IdP를 추가하는 단추를 보여주는 스크린샷](media/direct-federation/new-saml-wsfed-idp.png)

4. **새 SAML/WS-Fed IdP** 페이지의 **ID 공급자 프로토콜** 아래에서 **SAML** 또는 **WS-FED** 를 선택합니다.

    ![SAML 또는 WS-Fed IdP 페이지의 구문 분석 단추를 보여주는 스크린샷](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 페더레이션의 대상 도메인 이름이 될 파트너 조직의 도메인 이름을 입력합니다.
6. 메타데이터 파일을 업로드하여 메타데이터 세부 정보를 채울 수 있습니다. 메타데이터를 수동으로 입력하도록 선택하는 경우 다음 정보를 입력합니다.
   - 파트너 IdP의 도메인 이름
   - 파트너 IdP의 엔터티 ID
   - 파트너 IdP의 수동 요청자 엔드포인트
   - 인증서
   > [!NOTE]
   > 메타데이터 URL은 선택 사항이지만 사용하는 것이 좋습니다. 메타데이터 URL을 제공하면 Azure AD에서 만료되는 서명 인증서를 자동으로 갱신할 수 있습니다. 어떤 이유로든 인증서를 만료 시간 전에 회전하거나 메타데이터 URL을 제공하지 않으면 Azure AD에서 인증서를 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트해야 합니다.

7. **저장** 을 선택합니다. 

### <a name="to-configure-samlws-fed-idp-federation-in-azure-ad-using-powershell"></a>PowerShell을 사용하여 Azure AD에서 SAML/WS-Fed IdP 페더레이션을 구성하려면 다음을 수행합니다.

1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). 자세한 단계가 필요한 경우 빠른 시작에 [PowerShell 모듈](b2b-quickstart-invite-powershell.md#prerequisites) 지침이 있습니다.
2. 다음 명령 실행:

   ```powershell
   Connect-AzureAD
   ```

3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.
4. 다음 명령을 실행하여 페더레이션 메타데이터 파일의 값을 바꿉니다. AD FS 서버 및 Okta의 경우 페더레이션 파일은 federationmetadata.xml입니다(예: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`). 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-4-test-samlws-fed-idp-federation-in-azure-ad"></a>4단계: Azure AD에서 SAML/WS-Fed IdP 페더레이션 테스트
이제 새 B2B 게스트 사용자를 초대하여 페더레이션 설정을 테스트합니다. 자세한 내용은 [Azure Portal에서 Azure AD B2B 협업 사용자 추가](add-users-administrator.md)를 참조하세요.
 
## <a name="how-do-i-edit-a-samlws-fed-idp-federation-relationship"></a>SAML/WS-Fed IdP 페더레이션 관계를 편집하려면 어떻게 할까요?

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. **SAML/WS-Fed ID 공급자** 아래에서 공급자를 선택합니다.
5. ID 공급자 세부 정보 창에서 값을 업데이트합니다.
6. **저장** 을 선택합니다.


## <a name="how-do-i-remove-federation"></a>페데레이션을 삭제하려면 어떻게 할까요?

페더레이션 설정을 제거할 수 있습니다. 이 설정을 제거하면 이미 초대에 응한 페더레이션 게스트 사용자는 로그인할 수 없습니다. 하지만 [해당 상환 상태를 재설정](reset-redemption-status.md)하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다. Azure AD 포털에서 IdP와의 페더레이션을 제거하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. ID 공급자를 선택한 다음, **삭제** 를 선택합니다.
5. **예** 를 선택하여 삭제를 확인합니다. 

PowerShell을 사용하여 ID 공급자와의 페더레이션을 제거하려면 다음을 수행합니다.

1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령 실행:

   ```powershell
   Connect-AzureAD
   ```

3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.
4. 다음 명령을 입력합니다.

   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>다음 단계

외부 사용자가 다양한 ID 공급자로 로그인하는 경우 [초대 상환 환경](redemption-experience.md)에 대해 자세히 알아보세요.
