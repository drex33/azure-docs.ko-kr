---
title: '자습서: Salesforce와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Salesforce 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: b5617c91bedcb0b399ffe1627e76692007e7ca30
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299699"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>자습서: Salesforce와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Salesforce를 연결하는 방법에 대해 알아봅니다. Azure AD와 Salesforce를 연결하면 다음을 수행할 수 있습니다.

* Salesforce에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Salesforce에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Salesforce SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Salesforce에서 **SP** 시작 SSO를 지원합니다.

* Salesforce에서 [**자동** 사용자 프로비저닝 및 프로비저닝 해제](salesforce-provisioning-tutorial.md)(권장)를 지원합니다.

* Salesforce에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

* 이제 Salesforce 모바일 애플리케이션을 Azure AD에서 SSO가 가능하도록 설정할 수 있습니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

## <a name="adding-salesforce-from-the-gallery"></a>갤러리에서 Salesforce 추가

Salesforce의 Azure AD 통합을 구성하려면 갤러리의 Salesforce를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Salesforce** 를 입력합니다.
1. 결과 패널에서 **Salesforce** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Salesforce에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Salesforce에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Salesforce의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Salesforce에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Salesforce SSO 구성](#configure-salesforce-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Salesforce 테스트 사용자 만들기](#create-salesforce-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Salesforce에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Salesforce** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.
    
    a. **식별자** 텍스트 상자에 다음 패턴으로 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`

    다. **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Salesforce 클라이언트 지원 팀](https://help.salesforce.com/support)에 문의하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Salesforce 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Salesforce에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Salesforce** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-salesforce-sso"></a>Salesforce SSO 구성

1. Salesforce 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Salesforce 설정** 을 클릭하면 Salesforce Single Sign-On 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Salesforce Single Sign-On에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~13단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Salesforce를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Salesforce 회사 사이트에 관리자 권한으로 로그인하여 다음 단계를 수행합니다.

1. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정** 을 클릭합니다.

    ![Single Sign-On 설정 구성 아이콘](./media/salesforce-tutorial/configure1.png)

1. 탐색 창에서 **설정** 으로 스크롤하고 **ID** 를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정** 을 클릭합니다.

    ![Single Sign-On 설정 구성](./media/salesforce-tutorial/sf-admin-sso.png)

1. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Single Sign-On 편집 구성](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce 계정에 Single Sign-On을 사용하도록 설정할 수 없는 경우 [Salesforce 클라이언트 지원 팀](https://help.salesforce.com/support)에 문의해야 합니다.

1. **SAML 사용** 을 선택한 다음 **저장** 을 클릭합니다.

    ![Single Sign-On SAML을 사용하도록 구성](./media/salesforce-tutorial/sf-enable-saml.png)

1. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기** 를 클릭합니다.

    ![메타데이터 파일에서 Single Sign-On 새로 구성](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. **파일 선택** 을 클릭하여 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드하고 **만들기** 를 클릭합니다.

    ![Single Sign-On 선택 파일 구성](./media/salesforce-tutorial/xmlchoose.png)

1. **SAML Single Sign-On 설정** 페이지에서 필드가 자동으로 채워집니다. SAML JIT를 사용하려면 **사용자 프로비저닝 사용** 을 선택하고, **어설션에 사용자 개체의 페더레이션 ID가 포함** 되어 있으므로 **SAML ID 형식** 을 선택합니다. 그렇지 않으면 **사용자 프로비저닝 사용** 을 선택 취소하고, **어설션에 사용자의 Salesforce 사용자 이름이 포함** 되어 있으므로 **SAML ID 형식** 을 선택합니다. **저장** 을 클릭합니다.

    ![Single Sign-On 사용자 프로비저닝을 사용하도록 구성](./media/salesforce-tutorial/salesforcexml.png)

    > [!NOTE]
    > SAML JIT를 구성한 경우 **[Azure AD SSO 구성](#configure-azure-ad-sso)** 섹션에서 추가 단계를 완료해야 합니다. Salesforce 애플리케이션에는 특정 SAML 어설션이 필요하므로 특정 특성이 SAML 토큰 특성 구성에 있어야 합니다. 다음 스크린샷에서는 Salesforce의 필수 특성 목록을 보여 줍니다.
    
    ![JIT 필수 특성 창을 보여 주는 스크린샷](./media/salesforce-tutorial/just-in-time-attributes-required.png)
    
    SAML JIT를 사용하여 사용자를 프로비전하는 데 여전히 문제가 있는 경우 [Just-In-Time 프로비전 요구 사항 및 SAML 어설션 필드](https://help.salesforce.com/s/articleView?id=sf.sso_jit_requirements.htm&type=5)를 참조하세요. 일반적으로 JIT가 실패하면 `We can't log you in because of an issue with single sign-on. Contact your Salesforce admin for help.`와 같은 오류가 표시될 수 있습니다.


1. Salesforce의 왼쪽 탐색 패널에서 **회사 설정** 을 클릭하여 관련 섹션을 확장하고 **내 도메인** 을 클릭합니다.

    ![내 도메인에 Single Sign-On 구성](./media/salesforce-tutorial/sf-my-domain.png)

1. **인증 구성** 섹션으로 스크롤하여 **편집** 단추를 클릭합니다.

    ![Single Sign-On 인증 구성 구성](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. **인증 구성** 섹션에서 SAML SSO 구성의 **인증 서비스** 로 **로그인 페이지** 및 **AzureSSO** 를 선택한 다음, **저장** 을 클릭합니다.

    ![Single Sign-On 인증 서비스 구성](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > 둘 이상의 인증 서비스를 선택하는 경우 사용자가 Salesforce 환경으로 Single Sign-On을 시작하면 로그인하려는 인증 서비스를 선택하라는 메시지가 표시됩니다. 이 메시지가 표시되지 않도록 하려면 **다른 모든 인증 서비스를 선택하지 않은 상태로 유지** 해야 합니다.

### <a name="create-salesforce-test-user"></a>Salesforce 테스트 사용자 만들기

이 섹션에서는 Salesforce에서 B.Simon이라는 사용자를 만듭니다. Salesforce는 기본적으로 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Salesforce에 사용자가 없는 경우 Salesforce에 액세스하려고 시도하면 새 사용자가 만들어집니다. Salesforce는 자동 사용자 프로비전도 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](salesforce-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Salesforce 로그온 URL로 리디렉션됩니다. 

* Salesforce 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱 포털에서 Salesforce 타일을 클릭하면 SSO를 설정한 Salesforce에 자동으로 로그인됩니다. 내 앱 포털에 대한 자세한 내용은 [내 앱 포털 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce에 대한 SSO 테스트(모바일)

1. Salesforce 모바일 애플리케이션을 엽니다. 로그인 페이지에서 **Use Custom Domain(사용자 지정 도메인)** 을 클릭합니다.

    ![Salesforce 모바일 앱 사용자 지정 도메인 사용](media/salesforce-tutorial/mobile-app1.png)

1. **사용자 지정 도메인** 텍스트 상자에 등록된 사용자 지정 도메인 이름을 입력하고 **계속** 을 클릭합니다.

    ![Salesforce 모바일 앱 사용자 지정 도메인](media/salesforce-tutorial/mobile-app2.png)

1. Salesforce 애플리케이션에 로그인하기 위해 Azure AD 자격 증명을 입력하고 **다음** 을 클릭합니다.

    ![Salesforce 모바일 앱 Azure AD 자격 증명](media/salesforce-tutorial/mobile-app3.png)

1. 아래와 같이 **액세스 허용** 페이지에서 **허용** 을 클릭하여 Salesforce 애플리케이션에 대한 액세스 권한을 부여합니다.

    ![Salesforce 모바일 앱 액세스 허용](media/salesforce-tutorial/mobile-app4.png)

1. 마지막으로 로그인에 성공하면 애플리케이션 홈페이지가 표시됩니다.

    ![Salesforce 모바일 앱 홈페이지](media/salesforce-tutorial/mobile-app5.png) ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>다음 단계

Salesforce가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
