---
title: '자습서: AlertOps와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 AlertOps 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: jeedes
ms.openlocfilehash: 0c40d5f65148bc21ef9f4704023f9d1bfbd70db3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132330798"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>자습서: Azure Active Directory와 AlertOps 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AlertOps를 통합하는 방법에 대해 알아봅니다. Azure AD와 AlertOps를 통합하면 다음 작업을 수행할 수 있습니다.

* Azure AD에서 AlertOps에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 AlertOps에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AlertOps SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* AlertOps는 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-alertops-from-the-gallery"></a>갤러리에서 AlertOps 추가

AlertOps의 Azure AD 통합을 구성하려면 갤러리의 AlertOps를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AlertOps** 를 입력합니다.
1. 결과 패널에서 **AlertOps** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-alertops"></a>AlertOps에 대한 Azure AD SSO 구성 및 테스트

테스트 사용자 **B.Simon** 을 사용하여 AlertOps로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AlertOps의 관련 사용자 간에 연결 관계를 설정해야 합니다.

AlertOps에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행하세요.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AlertOps SSO 구성](#configure-alertops-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[AlertOps 테스트 사용자 만들기](#create-alertops-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 AlertOps에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **AlertOps** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자** 텍스트 상자에서 `https://<SUBDOMAIN>.alertops.com` 패턴을 사용하여 URL을 입력합니다.

    1. **회신 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.alertops.com/login.aspx` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.alertops.com/login.aspx` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [AlertOps 클라이언트 지원팀](mailto:support@alertops.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **AlertOps 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `BrittaSimon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 AlertOps에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **AlertOps** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-alertops-sso"></a>AlertOps SSO 구성

1. AlertOps 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **AlertOps 설정** 을 클릭하면 AlertOps 애플리케이션으로 이동됩니다. 여기에서 관리자 자격 증명을 입력하여 AlertOps에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. AlertOps를 수동으로 설정하려면 새 웹 브라우저 창을 열고 AlertOps 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. 왼쪽 탐색 패널에서 **계정 설정** 을 클릭합니다.

    ![스크린샷은 계정 설정이 호출된 AlertOps 메뉴를 보여줍니다.](./media/alertops-tutorial/settings.png)

5. **구독 설정** 페이지에서 **SSO** 를 선택하고 다음 단계를 수행합니다.

    ![스크린샷은 이 단계에서 설명한 대로 값이 입력된 SSO에 대한 구독 설정 창을 보여줍니다.](./media/alertops-tutorial/configuration.png)

    a. **SSO(Single Sign-On) 사용** 확인란을 선택합니다.

    b. 드롭다운 목록에서 **Azure Active Directory** 를 **SSO 공급자** 로 선택합니다.

    다. **발급자 URL** 텍스트 상자에서 Azure Portal의 **기본 SAML 구성** 섹션에서 사용한 ID 값을 사용합니다.

    d. **SAML 엔드포인트 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    e. **SLO 엔드포인트 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    f. 드롭다운 목록에서 **SHA256** 을 **SAML 서명 알고리즘** 으로 선택합니다.

    g. 다운로드한 인증서(Base64) 파일을 메모장에서 엽니다. 콘텐츠를 클립보드로 복사한 다음, X.509 인증서 텍스트 상자에 붙여넣습니다.

### <a name="create-alertops-test-user"></a>AlertOps 테스트 사용자 만들기

1. 다른 브라우저 창에서 AlertOps 회사 사이트에 관리자 권한으로 로그온합니다.

2. 왼쪽 탐색 패널에서 **사용자** 를 클릭합니다.

    ![스크린샷은 사용자가 호출된 AlertOps 메뉴를 보여줍니다.](./media/alertops-tutorial/user.png)

3. **사용자 추가** 를 선택합니다.

    ![스크린샷은 사용자 추가 단추가 있는 사용자 창을 보여줍니다.](./media/alertops-tutorial/add-user.png)

4. **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.

    ![스크린샷은 이 단계에서 설명하는 대로 값이 입력된 사용자 추가 창을 보여줍니다.](./media/alertops-tutorial/add-values.png)

    a. **로그인 사용자 이름** 텍스트 상자에 **Brittasimon** 과 같은 사용자 이름을 입력합니다.

    b. **공식 이메일** 텍스트 상자에 **Brittasimon\@contoso.com** 과 같은 사용자의 이메일 주소를 입력합니다.

    다. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    d. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    e. 드롭다운 목록에서 조직에 맞는 **형식** 값을 선택합니다.

    f. 드롭다운 목록에서 조직에 맞는 사용자의 **역할** 을 선택합니다.

    g. **추가** 를 선택합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 AlertOps 로그온 URL로 리디렉션됩니다.  

* AlertOps 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 AlertOps에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 AlertOps 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 AlertOps에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

AlertOps가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
