---
title: '자습서: Akamai와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Akamai 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 38b0ea6b65c3fcb01e8a49cd3a416533bcab5688
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>자습서: Akamai와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Akamai를 통합하는 방법에 대해 알아봅니다. Azure AD와 Akamai를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Akamai에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Akamai에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure Active Directory와 Akamai 엔터프라이즈 애플리케이션을 통합하면 클라우드 또는 온-프레미스에 호스트되는 레거시 애플리케이션에 원활하게 액세스할 수 있습니다. 통합 솔루션은 앱을 수정하거나 에이전트를 설치할 필요 없이 [Azure AD 조건부 액세스](../conditional-access/overview.md), [Azure AD ID 보호](../identity-protection/overview-identity-protection.md) 및 [Azure AD Identity Governance](../governance/identity-governance-overview.md)와 같은 Azure Active Directory의 모든 최신 기능을 레거시 애플리케이션에 활용합니다.

아래 이미지는 Akamai EAA가 광범위한 하이브리드 보안 액세스 시나리오에 적합하다는 것을 보여줍니다.

![광범위한 하이브리드 보안 액세스 시나리오에 적합한 Akamai EAA](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>키 인증 시나리오

Akamai EAA는 Open ID Connect, SAML, WS-Fed 등의 최신 인증 프로토콜을 사용할 수 있도록 Azure Active Directory 통합을 기본적으로 지원할 뿐 아니라, Azure AD를 통한 내부 및 외부 액세스를 위해 레거시 기반 인증 앱에 대한 보안 액세스를 확장하여 이러한 애플리케이션에서 최신 시나리오(예 : 암호 없는 액세스)를 가능하게 합니다. 다음 내용이 포함됩니다.

* 헤더 기반 인증 앱
* 원격 데스크톱
* SSH(Secure Shell)
* Kerberos 인증 앱
* VNC(가상 네트워크 컴퓨팅)
* 익명 인증 또는 기본 제공 인증 앱 없음
* NTLM 인증 앱(이중 프롬프트를 사용하여 사용자 보호)
* 양식 기반 애플리케이션(이중 프롬프트를 사용하여 사용자 보호)

### <a name="integration-scenarios"></a>통합 시나리오

파트너십을 체결한 Microsoft와 Akamai EAA는 고객의 비즈니스 요구 사항에 따라 여러 통합 시나리오를 지원하여 고객의 비즈니스 요구 사항을 유연하게 충족할 수 있습니다. 이를 통해 모든 애플리케이션에서 즉시 서비스를 제공할 수 있으며, 점진적으로 정책을 분류하여 적절한 정책 분류를 구성할 수 있습니다.

#### <a name="integration-scenario-1"></a>통합 시나리오 1

Akamai EAA는 Azure AD에서 단일 애플리케이션으로 구성됩니다. 관리자는 애플리케이션에서 조건부 액세스 정책을 구성할 수 있으며, 조건이 충족되면 사용자는 Akamai EAA 포털에 대한 액세스 권한을 얻을 수 있습니다.

**장점**:

* IDP를 한 번만 구성하면 됩니다.

**단점**:

* 사용자에게 제공되는 애플리케이션 포털이 최종적으로 2개입니다.

* 공통 조건부 액세스 정책 하나가 모든 애플리케이션에 적용됩니다.

![통합 시나리오 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>통합 시나리오 2

Akamai EAA 애플리케이션은 Azure AD 포털에서 개별적으로 설정합니다. 관리자는 애플리케이션에서 개별 조건부 액세스 정책을 구성할 수 있으며, 조건이 충족되면 사용자를 특정 애플리케이션으로 직접 리디렉션할 수 있습니다.

**장점**:

* 개별 CA 정책을 정의할 수 있습니다.

* 모든 앱이 0365 Waffle 및 myApps.microsoft.com 패널에 표시됩니다.


**단점**:

* IDP를 여러 개 구성해야 합니다.

![통합 시나리오 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Akamai SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Akamai에서 IDP 시작 SSO를 지원합니다.

#### <a name="important"></a>중요

아래에 나열된 모든 설정은 **통합 시나리오 1** 및 **시나리오 2** 에 동일하게 적용됩니다. **통합 시나리오 2** 의 경우 Akamai EAA에서 개별 IDP를 설정했으며, 애플리케이션 URL을 가리키도록 URL 속성을 수정해야 합니다.

![Akamai 엔터프라이즈 애플리케이션 액세스의 AZURESSO-SP에 대한 General(일반) 탭의 스크린샷 인증 구성 URL 필드가 강조 표시됩니다.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>갤러리에서 Akamai 추가

Akamai가 Azure AD에 통합되도록 구성하려면 갤러리의 Akamai를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Akamai** 를 입력합니다.
1. 결과 패널에서 **Akamai** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Akamai에 대한 Azure AD SSO 구성 및 테스트

**B. Simon** 이라는 테스트 사용자를 사용하여 Akamai에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Akamai의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Akamai에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Akamai SSO 구성](#configure-akamai-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[IDP 설정](#setting-up-idp)**
    * **[헤더 기반 인증](#header-based-authentication)**
    * **[원격 데스크톱](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos 인증](#kerberos-authentication)**
    * **[Akamai 테스트 사용자 만들기](#create-akamai-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Akamai에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Akamai** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Akamai 클라이언트 지원 팀](https://www.akamai.com/us/en/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Akamai 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Akamai에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Akamai** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-akamai-sso"></a>Akamai SSO 구성

### <a name="setting-up-idp"></a>IDP 설정

**AKAMAI EAA IDP 구성**

1. **Akamai 엔터프라이즈 애플리케이션 액세스** 콘솔에 로그인합니다.
1. **Akamai EAA 콘솔** 에서 **ID** > **ID 공급자** 를 선택하고 **ID 공급자 추가** 를 클릭합니다.

    ![Akamai EAA 콘솔 ID 공급자 창의 스크린샷 ID 메뉴에서 ID 공급자를 선택하고 ID 공급자 추가를 선택합니다.](./media/header-akamai-tutorial/configure-1.png)

1. **새 ID 공급자 만들기** 에서 다음 단계를 수행합니다.

    ![Akamai EAA 콘솔의 Create New Identity Providers(새 ID 공급자 만들기) 대화 상자의 스크린샷](./media/header-akamai-tutorial/configure-2.png)

    a. **Unique Name**(고유 이름)을 지정합니다.

    b. **Third Party SAML**(타사 SAML)을 선택하고, **Create Identity Provider and Configure**(ID 공급자 만들기 및 구성)를 클릭합니다.

### <a name="general-settings"></a>General Settings(일반 설정)

1. **Identity Intercept**(ID 가로채기) - Azure AD 구성에 사용되는 SP 기본 URL의 이름을 지정합니다.

    > [!NOTE]
    > 고유한 사용자 지정 도메인을 사용하도록 선택할 수 있습니다(DNS 항목과 인증서가 필요함). 이 예에서는 Akamai 도메인을 사용하고 있습니다.

1. **Akamai Cloud Zone**(Akamai 클라우드 영역) - 적절한 클라우드 영역을 선택합니다.
1. **Certificate Validation**(인증서 유효성 검사) - Akamai 설명서를 확인합니다(선택 사항).

    ![Identity Intercept(ID 가로채기), Akamai Cloud Zone(Akamai 클라우드 영역) 및 Certificate Validation(인증서 유효성 검사)에 대한 설정을 보여주는 Akamai EAA 콘솔 General(일반) 탭의 스크린샷](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Authentication Configuration(인증 구성)

1. URL – ID 가로채기와 동일한 URL(인증 후 사용자가 리디렉션되는 위치임)을 지정합니다.
2. Logout URL(로그아웃 URL): 로그아웃 URL을 업데이트합니다.
3. Sign SAML Request(SAML 요청 서명): 기본적으로 선택 취소되어 있습니다.
4. IDP 메타데이터 파일의 경우 Azure AD 콘솔에서 애플리케이션을 추가합니다.

    ![URL, Logout URL(로그아웃 URL), Sign SAML Request(SAML 요청 서명), IDP 메타데이터 파일에 대한 설정을 보여주는 Akamai EAA 콘솔 Authentication configuration(인증 구성)의 스크린샷](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>세션 설정

설정을 기본값으로 둡니다.

![Akamai EAA 콘솔 Session settings(세션 설정) 대화 상자의 스크린샷](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>디렉터리

디렉터리 구성은 건너뜁니다.

![Akamai EAA 콘솔 Directories(디렉터리) 탭의 스크린샷](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>사용자 지정 UI

IDP에 사용자 지정을 추가할 수 있습니다.

![Customize UI(UI 사용자 지정), Language settings(언어 설정), Themes(테마)에 대한 설정을 보여주는 Akamai EAA 콘솔 Customization(사용자 지정) 탭의 스크린샷](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>고급 설정

[고급 설정]은 건너뜁니다. 자세한 내용은 Akamai 설명서를 참조하세요.

![EAA Client(EAA 클라이언트), Advanced(고급), OIDC to SAML bridging(OIDC-SAML 브리징)에 대한 설정을 보여주는 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>배포

1. [ID 공급자 배포]를 클릭합니다.

    ![Deploy Identity provider(ID 공급자 배포) 단추를 보여주는 Akamai EAA 콘솔 Deployment(배포) 탭의 스크린샷](./media/header-akamai-tutorial/deployment.png)

2. 배포가 성공했는지 확인합니다.

### <a name="header-based-authentication"></a>헤더 기반 인증

Akamai 헤더 기반 인증

1. Add Applications(애플리케이션 추가) 마법사에서 **Custom HTTP**(사용자 지정 HTTP)를 선택합니다.

    ![Access Apps(앱 액세스) 섹션에 나열된 CustomHTTP(사용자 지정 HTTP)를 보여주는 Akamai EAA 콘솔 Add Applications(애플리케이션 추가) 마법사의 스크린샷](./media/header-akamai-tutorial/configure-5.png)

2. **애플리케이션 이름** 및 **설명** 을 입력합니다.

    ![애플리케이션 이름 및 설명에 대한 설정을 보여주는 Custom HTTP App(사용자 지정 HTTP 앱) 대화 상자의 스크린샷](./media/header-akamai-tutorial/configure-6.png)

    ![MYHEADERAPP의 일반 설정을 보여주는 Akamai EAA 콘솔 General(일반) 탭의 스크린샷](./media/header-akamai-tutorial/configure-7.png)

    ![인증서 및 위치 설정을 보여주는 Akamai EAA 콘솔의 스크린샷](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>인증

1. **인증** 탭을 선택합니다.

    ![Authentication(인증) 탭이 선택된 Akamai EAA 콘솔의 스크린샷](./media/header-akamai-tutorial/configure-9.png)

2. **ID 공급자** 를 할당합니다.

    ![Azure AD SSO로 설정된 ID 공급자를 보여주는 MYHEADERAPP의 Akamai EAA 콘솔 Authentication(인증) 탭의 스크린샷](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Services

[Save and Go to Authentication]\(저장 후 인증으로 이동)을 클릭합니다.

![오른쪽 아래 모서리에 있는 Save and go to AdvancedSettings(저장 후 고급 설정으로 이동) 단추를 보여주는 MYHEADERAPP의 Akamai EAA 콘솔 Services(서비스) 탭의 스크린샷](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>고급 설정

1. **Customer HTTP Headers**(고객 HTTP 헤더)에서 **CustomerHeader** 및 **SAML Attribute**(SAML 특성)를 지정합니다.

    ![Authentication(인증) 아래 강조 표시된 SSO Logged URL(SSO 로그 URL) 필드를 보여주는 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/configure-12.png)

1. **Save and go to Deployment**(저장 후 배포로 이동) 단추를 클릭합니다.

    ![오른쪽 아래 모서리에 있는 Save and go to Deployment(저장 후 배포로 이동) 단추를 보여주는 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>애플리케이션 배포

1. **Deploy Application**(애플리케이션 배포) 단추를 클릭합니다.

    ![Deploy application(애플리케이션 배포) 단추를 보여주는 Akamai EAA 콘솔 Deployment(배포) 탭의 스크린샷](./media/header-akamai-tutorial/configure-14.png)

1. 애플리케이션이 성공적으로 배포되었는지 확인합니다.

    ![애플리케이션 상태 메시지를 보여주는 Akamai EAA 콘솔 Deployment(배포) 탭의 스크린샷: "Application Successfully Deployed".(애플리케이션을 배포했습니다.)](./media/header-akamai-tutorial/configure-15.png)

1. 최종 사용자 환경.

    ![배경 이미지 및 로그인 대화 상자가 있는 myapps.microsoft.com의 시작 화면 스크린샷](./media/header-akamai-tutorial/end-user-1.png)

    ![추가 기능, HRWEB, Akamai-CorpApps, 비용, 그룹 및 액세스 검토 아이콘이 있는 앱 창의 일부를 보여주는 스크린샷 ](./media/header-akamai-tutorial/end-user-2.png)

1. 조건부 액세스.

    ![메시지의 스크린샷: 로그인 요청 승인 모바일 디바이스에 알림을 보냈습니다. 계속하려면 응답하세요.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![MyHeaderApp 아이콘을 보여주는 애플리케이션 화면의 스크린샷](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>원격 데스크톱

1. ADD Applications(애플리케이션 추가) 마법사에서 **RDP** 를 선택합니다.

    ![Access Apps(앱 액세스) 섹션의 앱 중에 나열된 RDP를 보여주는 Akamai EAA 콘솔 Add Applications(애플리케이션 추가) 마법사의 스크린샷](./media/header-akamai-tutorial/configure-16.png)

1. **애플리케이션 이름** 및 **설명** 을 입력합니다.

    ![애플리케이션 이름 및 설명에 대한 설정을 보여주는 RDP App(RDP 앱) 대화 상자 스크린샷](./media/header-akamai-tutorial/configure-17.png)

    ![SECRETRDPAPP에 대한 Application identity(애플리케이션 ID) 설정을 보여주는 Akamai EAA 콘솔 General(일반) 탭의 스크린샷](./media/header-akamai-tutorial/configure-18.png)

1. 이 서비스를 제공할 커넥터를 지정합니다.

    ![인증서 및 위치 설정을 보여주는 Akamai EAA 콘솔의 스크린샷 연결된 커넥터는 USWST-CON1로 설정됩니다.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>인증

**Save and go to Services**(저장 후 서비스로 이동)를 클릭합니다.

![오른쪽 아래 모서리에 있는 Save and go to Services(저장 후 서비스로 이동) 단추를 보여주는 SECRETRDPAPP의 Akamai EAA 콘솔 Authentication(인증) 탭의 스크린샷](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Services

**Save and go to Advanced Settings**(저장 후 서비스로 이동)를 클릭합니다.

![오른쪽 아래 모서리에 있는 Save and go to AdvancedSettings(저장 후 고급 설정으로 이동) 단추를 보여주는 SECRETRDPAPP의 Akamai EAA 콘솔 Services(서비스) 탭의 스크린샷](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>고급 설정

1. **Save and go to Deployment**(저장 후 배포로 이동)를 클릭합니다.

    ![Remote desktop configuration(원격 데스크톱 구성) 설정을 보여주는 SECRETRDPAPP의 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/configure-22.png)

    ![Authentication(인증) 및 Health check configuration(상태 검사 구성) 설정을 보여주는 SECRETRDPAPP의 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/configure-23.png)

    ![오른쪽 아래 모서리에 Save and go to Deployment(저장 후 배포로 이동) 단추가 있는 SECRETRDPAPP의 Akamai EAA 콘솔 Custom HTTP headers(사용자 지정 HTTP 헤더) 설정의 스크린샷](./media/header-akamai-tutorial/configure-24.png)

1. 최종 사용자 환경

    ![배경 이미지와 로그인 대화 상자가 있는 myapps.microsoft.com 창의 스크린샷](./media/header-akamai-tutorial/end-user-3.png)

    ![추가 기능, HRWEB, Akamai - CorpApps, 비용, 그룹 및 액세스 검토 아이콘이 있는 myapps.microsoft.com 앱 창의 스크린샷](./media/header-akamai-tutorial/end-user-2.png)

1. 조건부 액세스

    ![조건부 액세스 메시지의 스크린샷: 로그인 요청 승인 모바일 디바이스에 알림을 보냈습니다. 계속하려면 응답하세요.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp 및 SecretRDPApp 아이콘을 보여주는 애플리케이션 화면의 스크린샷](./media/header-akamai-tutorial/conditional-access-5.png)

    ![일반 사용자 아이콘을 보여주는 Windows Server 2012 RS 화면 스크린샷 관리자, user0 및 user1의 아이콘은 로그인된 것으로 표시됩니다.](./media/header-akamai-tutorial/conditional-access-6.png)

1. RDP 애플리케이션 URL을 직접 입력할 수도 있습니다.

#### <a name="ssh"></a>SSH

1. [애플리케이션 추가]로 이동하여 **SSH** 를 선택합니다.

    ![Access Apps(앱 액세스) 섹션의 앱 중에 나열된 SSH를 보여주는 Akamai EAA 콘솔 Add Applications(애플리케이션 추가) 마법사의 스크린샷](./media/header-akamai-tutorial/configure-25.png)

1. **애플리케이션 이름** 및 **설명** 을 입력합니다.

    ![애플리케이션 이름 및 설명에 대한 설정을 보여주는 SSH App(SSH 앱) 대화 상자의 스크린샷](./media/header-akamai-tutorial/configure-26.png)

1. [Application Identity]\(애플리케이션 ID)를 구성합니다.

    ![SSH-SECURE에 대한 Application identity(애플리케이션 ID) 설정을 보여주는 Akamai EAA 콘솔 General(일반) 탭의 스크린샷](./media/header-akamai-tutorial/configure-27.png)

    a. [Name/Description]\(이름/설명)을 지정합니다.

    b. SSH에 대한 [Application Server IP/FQDN]\(애플리케이션 서버 IP/FQDN) 및 포트를 지정합니다.

    다. SSH 사용자 이름/암호를 지정합니다. *Akamai EAA를 확인하세요.

    d. [External host Name]\(외부 호스트 이름)을 지정합니다.

    e. 커넥터의 [Location]\(위치)을 지정하고, 커넥터를 선택합니다.

#### <a name="authentication"></a>인증

**Save and go to Services**(저장 후 서비스로 이동)를 클릭합니다.

![오른쪽 아래 모서리에 있는 Save and go to Services(저장 후 서비스로 이동) 단추를 보여주는 SSH-SECURE의 Akamai EAA 콘솔 Authentication(인증) 탭의 스크린샷](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Services

**Save and go to Advanced Settings**(저장 후 서비스로 이동)를 클릭합니다.

![오른쪽 아래 모서리에 있는 Save and go to AdvancedSettings(저장 후 고급 설정으로 이동) 단추를 보여주는 SSH-SECURE의 Akamai EAA 콘솔 Services(서비스) 탭의 스크린샷](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>고급 설정

저장을 클릭하고 배포로 이동합니다.

![Authentication(인증) 및 Health check configuration(상태 검사 구성) 설정을 보여주는 SSH-SECURE의 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/configure-30.png)

![오른쪽 아래 모서리에 Save and go to Deployment(저장 후 배포로 이동) 단추가 있는 SSH-SECURE의 Akamai EAA 콘솔 Custom HTTP headers(사용자 지정 HTTP 헤더) 설정의 스크린샷](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>배포

1. **Deploy Application**(애플리케이션 배포)을 클릭합니다.

    ![Deploy application(애플리케이션 배포) 단추를 보여주는 SSH-SECURE의 Akamai EAA 콘솔 Deployment(배포) 탭의 스크린샷](./media/header-akamai-tutorial/configure-32.png)

1. 최종 사용자 환경

    ![myapps.microsoft.com 창 로그인 대화 상자의 스크린샷](./media/header-akamai-tutorial/end-user-3.png)

    ![추가 기능, HRWEB, Akamai - CorpApps, 비용, 그룹 및 액세스 검토 아이콘을 보여주는 myapps.microsoft.com의 앱 창 스크린샷](./media/header-akamai-tutorial/end-user-4.png)

1. 조건부 액세스

    ![메시지를 보여주는 스크린샷: 로그인 요청 승인 모바일 디바이스에 알림을 보냈습니다. 계속하려면 응답하세요.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp, SSH Secure, SecretRDPApp 아이콘을 보여주는 애플리케이션 화면의 스크린샷](./media/header-akamai-tutorial/conditional-access-7.png)

    ![암호 프롬프트를 보여주는 ssh-secure-go.akamai-access.com에 대한 명령 창의 스크린샷](./media/header-akamai-tutorial/conditional-access-8.png)

    ![애플리케이션에 대한 정보를 보여주고 명령 프롬프트를 표시하는 ssh-secure-go.akamai-access.com에 대한 명령 창의 스크린샷](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos 인증

아래 예제에서는 KCD를 사용하여 내부 웹 서버 <code>http://frp-app1.superdemo.live</code>를 게시하고 SSO를 사용하도록 설정합니다.

#### <a name="general-tab"></a>일반 탭

![MYKERBOROSAPP의 Akamai EAA 콘솔 General(일반) 탭의 스크린샷](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>[인증] 탭

ID 공급자를 할당합니다.

![Azure AD SSO로 설정된 ID 공급자를 보여주는 MYKERBOROSAPP의 Akamai EAA 콘솔 Authentication(인증) 탭의 스크린샷](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>서비스 탭

![MYKERBOROSAPP의 Akamai EAA 콘솔 Services(서비스) 탭의 스크린샷](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>고급 설정

![Related Applications(관련 애플리케이션) 및 Authentication(인증)에 대한 설정을 보여주는 MYKERBOROSAPP의 Akamai EAA 콘솔 Advanced Settings(고급 설정) 탭의 스크린샷](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> 웹 서버의 SPN은 SPN@Domain 형식이어야 합니다. 예를 들어 이 데모에서는 `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`입니다. 나머지 설정은 기본값으로 둡니다.

#### <a name="deployment-tab"></a>배포 탭

![Deploy application(애플리케이션 배포) 단추를 보여주는 MYKERBOROSAPP의 Akamai EAA 콘솔 Deployment(배포) 탭의 스크린샷](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>디렉터리 추가

1. 드롭다운에서 **AD** 를 선택합니다.

    ![Directory Type(디렉터리 유형) 드롭다운에 AD가 선택된 Create New Directory(새 디렉터리 만들기) 대화 상자를 보여주는 Akamai EAA 콘솔 Directories(디렉터리) 창의 스크린샷](./media/header-akamai-tutorial/configure-33.png)

1. 필요한 데이터를 제공합니다.

    ![DirectoryName(디렉터리 이름), Directory Service(디렉터리 서비스), Connector(커넥터) 및 Attribute mapping(특성 매핑) 설정이 있는 Akamai EAA 콘솔 SUPERDEMOLIVE 창의 스크린샷](./media/header-akamai-tutorial/configure-34.png)

1. 디렉터리가 만들어졌는지 확인합니다.

    ![superdemo.live 디렉터리가 추가되었음을 보여주는 Akamai EAA 콘솔 Directories(디렉터리) 창의 스크린샷](./media/header-akamai-tutorial/directory-domain.png)

1. 액세스 권한이 필요한 그룹/OU를 추가합니다.

    ![superdemo.live 디렉터리에 대한 설정 스크린샷 그룹 또는 OU를 추가하기 위해 선택한 아이콘이 강조 표시됩니다.](./media/header-akamai-tutorial/add-group.png)

1. 아래에서 그룹은 EAAGroup이고 1개 멤버가 포함되어 있습니다.

    ![Akamai EAA 콘솔 GROUPS ON SUPERDEMOLIVE DIRECTORY 창의 스크린샷 사용자 1명이 그룹 아래에 있는 EAAGroup](./media/header-akamai-tutorial/eaagroup.png)

1. **ID** > **ID 공급자** 를 클릭하고 **디렉터리** 탭을 클릭한 다음, **디렉터리 할당** 을 클릭합니다.

    ![Currently assigned directories(현재 할당된 디렉터리) 목록의 superdemo.live를 보여주는 Azure AD SSO의 Akamai EAA 콘솔 Directories(디렉터리) 탭의 스크린샷](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>EAA에 대한 KCD 위임 구성 연습

#### <a name="step-1-create-an-account"></a>1단계: 계정 만들기 

1. 이 예제에서는 **EAADelegation** 이라는 계정을 사용합니다. 이 연습은 **Active Directory 사용자 및 컴퓨터** 스내핀을 사용하여 수행할 수 있습니다.

    ![Azure AD SSO의 Akamai EAA 콘솔 Directories(디렉터리) 탭의 스크린샷 superdemo.live 디렉터리는 Currently assigned directories(현재 할당된 디렉터리) 아래에 나열됩니다.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > 사용자 이름은 **ID 가로채기 이름** 을 기반으로 하는 특정 형식이어야 합니다. 그림 1을 보면 **corpapps.login.go.akamai-access.com** 입니다.

1. 사용자 로그온 이름은 `HTTP/corpapps.login.go.akamai-access.com`입니다.

    ![이름이 "EAADelegation"으로 설정되고 사용자 로그온 이름이 HTTP/corpapps.login.go.akamai-access.com으로 설정된 EAADelegation 속성을 보여주는 스크린샷](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>2단계: 이 계정의 SPN을 설정합니다.

1. 이 샘플을 기반으로 하는 SPN은 다음과 같습니다.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation 명령의 결과를 보여주는 관리자 명령 프롬프트의 스크린샷](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>3단계: 위임 구성

1. EAADelegation 계정에 대한 [위임] 탭을 클릭합니다.

    ![SPN을 구성하는 명령을 보여주는 관리자 명령 프롬프트의 스크린샷](./media/header-akamai-tutorial/spn.png)

    * [모든 인증 프로토콜 사용]을 지정합니다.
    * [추가]를 클릭하고 Kerberos 웹 사이트의 앱 풀 계정을 추가합니다. 올바르게 구성되었다면 올바른 SPN으로 자동 확인됩니다.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>4단계: AKAMAI EAA에 대한 Keytab 파일 만들기

1. 다음은 일반 구문입니다.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. 예제 설명

    | 코드 조각 | 설명 |
    | - | - |
    | Ktpass /out EAADemo.keytab | // 출력 Keytab 파일의 이름 |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA 위임 계정 |
    | /pass RANDOMPASS | // EAA 위임 계정 암호 |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Akamai EAA 설명서 참조 |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![AKAMAI EAA에 대한 Keytab 파일을 만드는 명령의 결과를 보여주는 관리자 명령 프롬프트의 스크린샷](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>5단계: AKAMAI EAA 콘솔에서 Keytab 가져오기

1. **시스템** > **Keytabs** 를 클릭합니다.

    ![시스템 메뉴에서 선택된 Keytab을 보여주는 Akamai EAA 콘솔의 스크린샷](./media/header-akamai-tutorial/keytabs.png)

1. [Keytab 형식]에서 **Kerberos 위임** 을 선택합니다.

    ![Keytab 설정을 보여주는 Akamai EAA 콘솔 EAAKEYTAB 화면의 스크린샷 Keytab Type(Keytab 형식)은 Kerberos 위임으로 설정됩니다.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Keytab가 [배포됨] 및 [확인됨]으로 표시되는지 확인합니다.

    ![EAA Keytab이 "Keytab deployed and verified"(Keytab 배포 및 확인됨)로 나열된 Akamai EAA 콘솔 KEYTABS 화면의 스크린샷](./media/header-akamai-tutorial/keytabs-2.png)

1. 사용자 환경

    ![myapps.microsoft.com의 로그인 대화 상자 스크린샷 ](./media/header-akamai-tutorial/end-user-3.png)

    ![앱 아이콘이 보이는 myapps.microsoft.com의 앱 창 스크린샷](./media/header-akamai-tutorial/end-user-4.png)

1. 조건부 액세스

    ![로그인 요청 승인 메시지를 보여주는 스크린샷 메시지입니다.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp, SSH Secure, SecretRDPApp 및 myKerberosApp 아이콘을 보여주는 애플리케이션 화면의 스크린샷](./media/header-akamai-tutorial/conditional-access-10.png)

    ![myKerberosApp의 시작 화면 스크린샷 배경 이미지 위에 "Welcome superdemo\user1" 메시지가 표시됩니다.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Akamai 테스트 사용자 만들기

이 섹션에서는 Akamai에서 B.Simon이라는 사용자를 만듭니다. Akamai 플랫폼에서 사용자를 추가하려면 [Akamai 클라이언트 지원 팀](https://www.akamai.com/us/en/contact-us/)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Akamai에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Akamai 타일을 클릭하면 SSO를 설정한 Akamai에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Akamai가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).