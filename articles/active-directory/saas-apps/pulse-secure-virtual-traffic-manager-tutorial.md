---
title: '자습서: Pulse Secure Virtual Traffic Manager와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Pulse Secure Virtual Traffic Manager 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: dc4410758a5497ec22b9a80a1b110726a9fb45e7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>자습서: Pulse Secure Virtual Traffic Manager와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pulse Secure Virtual Traffic Manager를 통합하는 방법에 대해 알아봅니다. Azure AD와 Pulse Secure Virtual Traffic Manager를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Pulse Secure Virtual Traffic Manager에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Pulse Secure Virtual Traffic Manager에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Pulse Secure Virtual Traffic Manager SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Pulse Secure Virtual Traffic Manager에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>갤러리에서 Pulse Secure Virtual Traffic Manager 추가

Pulse Secure Virtual Traffic Manager가 Azure AD에 통합되도록 구성하려면 갤러리의 Pulse Secure Virtual Traffic Manager를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Pulse Secure Virtual Traffic Manager** 를 입력합니다.
1. 결과 패널에서 **Pulse Secure Virtual Traffic Manager** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Pulse Secure Virtual Traffic Manager에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Pulse Secure Virtual Traffic Manager에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Pulse Secure Virtual Traffic Manager의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Pulse Secure Virtual Traffic Manager에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Pulse Secure Virtual Traffic Manager SSO 구성](#configure-pulse-secure-virtual-traffic-manager-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Pulse Secure Virtual Traffic Manager 테스트 사용자 만들기](#create-pulse-secure-virtual-traffic-manager-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Pulse Secure Virtual Traffic Manager에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Pulse Secure Virtual Traffic Manager** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/consume` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/metadata` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/consume` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 회신 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Pulse Secure Virtual Traffic Manager 클라이언트 지원 팀](mailto:support@pulsesecure.net)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Pulse Secure Virtual Traffic Manager 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon에게 Pulse Secure Virtual Traffic Manager에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Pulse Secure Virtual Traffic Manager** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Pulse Secure Virtual Traffic Manager SSO 구성

이 섹션에서는 Pulse Virtual Traffic Manager에서 Azure AD SAML 인증을 사용하도록 설정하는 데 필요한 구성에 대해 설명합니다. 모든 구성은 관리 웹 UI를 사용하여 Pulse Virtual Traffic Manager에서 변경됩니다. 

### <a name="create-a-saml-trusted-identity-provider"></a>SAML 신뢰할 수 있는 ID 공급자 만들기

a. **Pulse Virtual Traffic Manager Appliance Admin UI(어플라이언스 관리 UI) > Catalog(카탈로그) > SAML > Trusted Identity Providers Catalog(신뢰할 수 있는 ID 공급자 카탈로그)** 페이지로 차례로 이동하여 **Edit(편집)** 를 클릭합니다.

![SAML 카탈로그 페이지](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. 새 SAML 신뢰할 수 있는 ID 공급자에 대한 세부 정보를 추가하고, Single Sign-On 설정 페이지 아래에서 Azure AD 엔터프라이즈 애플리케이션의 정보를 복사한 다음, **Create New Trusted Identity Provider(새 신뢰할 수 있는 ID 공급자 만들기)** 를 클릭합니다.

![새 신뢰할 수 있는 ID 공급자 만들기](./media/pulse-secure-virtual-traffic-manager-tutorial/identity-provider.png)

* **Name(이름)** 텍스트 상자에서 신뢰할 수 있는 ID 공급자의 이름을 입력합니다. 

* Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Entity_id** 텍스트 상자에 붙여넣습니다.  

* Azure Portal에서 복사한 **로그인 URL** 값을 **Url** 텍스트 상자에 붙여넣습니다. 

* Azure Portal에서 다운로드한 **인증서** 를 메모장으로 열고, 콘텐츠를 **Certificate(인증서)** 텍스트 상자에 붙여넣습니다.

다. 새 SAML ID 공급자가 성공적으로 만들어졌는지 확인합니다. 

![신뢰할 수 있는 ID 공급자 확인](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-identity-provider.png)

### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Azure AD 인증을 사용하도록 가상 서버 구성

a. **Pulse Virtual Traffic Manager Appliance Admin UI(어플라이언스 관리 UI) > Services(서비스) > Virtual Servers(가상 서버)** 페이지로 차례로 이동하여 이전에 만든 가상 서버 옆에 있는 **Edit(편집)** 를 클릭합니다.

![가상 서버 편집](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. **Authentication(인증)** 섹션에서 **Edit(편집)** 를 클릭합니다. 

![인증 섹션](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

다. 가상 서버에 대해 다음 인증 설정을 구성합니다. 

1. Authentication(인증) -

    ![가상 서버에 대한 인증 설정](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. **Auth!type** 에서 **SAML Service Provider(SAML 서비스 공급자)** 를 선택합니다. 

    b. **Auth!verbose** 에서 인증 문제를 해결하려면 "Yes(예)"로 설정하고, 그렇지 않으면 기본값을 "No(아니요)"로 둡니다. 

2. Authentication Session Management(인증 세션 관리) -

    ![Authentication Session Management](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. **Auth!session!cookie_name** 에 대해 기본값을 "VS_SamlSP_Auth"로 둡니다. 

    b. **auth!session!timeout** 에 대해 기본값을 "7200"으로 둡니다. 

    다. **auth!session!log_external_state** 에서 인증 문제를 해결하려면 "Yes(예)"로 설정하고, 그렇지 않으면 기본값을 "No(아니요)"로 둡니다. 

    d. **auth!session!cookie_attributes** 에서 "HTTPOnly"로 변경합니다.

3. SAML Service Provider(SAML 서비스 공급자) -

    ![SAML Service Provider](./media/pulse-secure-virtual-traffic-manager-tutorial/service-provider.png)

    a. **auth!saml!sp_entity_id** 텍스트 상자에서 Azure AD Single Sign-On 구성 식별자(엔터티 ID)로 사용하는 것과 동일한 URL로 설정합니다. `https://pulseweb.labb.info/saml/metadata`와 같습니다. 

    b. **auth!saml!sp_acs_url** 텍스트 상자에서 Azure AD Single Sign-On 회신 URL(Assertion Consumer Service URL)로 사용하는 것과 동일한 URL로 설정합니다. `https://pulseweb.labb.info/saml/consume`과 같습니다. 

    다. **auth!saml!idp** 에서 이전 단계에서 만든 **신뢰할 수 있는 ID 공급자** 를 선택합니다. 

    d. auth!saml!time_tolerance에서 기본값을 "5"초로 둡니다. 

    e. auth!saml!nameid_format에서 **unspecified(지정되지 않음)** 를 선택합니다.

    f. 페이지 아래쪽에서 **Update(업데이트)** 를 클릭하여 변경 내용을 적용합니다.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Pulse Secure Virtual Traffic Manager 테스트 사용자 만들기

이 섹션에서는 Pulse Secure Virtual Traffic Manager에서 Britta Simon이라는 사용자를 만듭니다. [Pulse Secure Virtual Traffic Manager 지원 팀](mailto:support@pulsesecure.net)과 협력하여 사용자를 Pulse Secure Virtual Traffic Manager 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Pulse Secure Virtual Traffic Manager 로그온 URL로 리디렉션됩니다. 

* Pulse Secure Virtual Traffic Manager 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Pulse Secure Virtual Traffic Manager 타일을 클릭하면 Pulse Secure Virtual Traffic Manager 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Pulse Secure Virtual Traffic Manager가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).