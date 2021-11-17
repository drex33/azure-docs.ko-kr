---
title: '자습서: Workday와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Workday 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 4ad86601abf86c11e899d481b9e981f810c9a6d1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>자습서: Workday와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Workday를 통합하는 방법에 대해 알아봅니다. Azure AD와 Workday를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Workday에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Workday에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Workday SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Workday에서 **SP** 시작 SSO를 지원합니다.

* 이제 Workday 모바일 애플리케이션을 Azure AD에서 SSO를 사용하도록 설정할 수 있습니다. 구성하는 방법에 대한 자세한 내용은 [이](workday-mobile-tutorial.md) 링크를 따라가세요.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-workday-from-the-gallery"></a>갤러리에서 Workday 추가

Workday가 Azure AD에 통합되도록 구성하려면 갤러리에서 Workday를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Workday** 를 입력합니다.
1. 결과 패널에서 **Workday** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Workday에 대한 Azure AD SSO 구성 및 테스트

**B. Simon** 이라는 테스트 사용자를 사용하여 Workday에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Workday의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Workday에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[Workday 구성](#configure-workday)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. **[Workday 테스트 사용자 만들기](#create-workday-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Workday에 만듭니다.
3. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Workday** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://impl.workday.com/<tenant>/login-saml2.flex` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://impl.workday.com/<tenant>/login-saml.htmld` 패턴을 사용하여 URL을 입력합니다.

    다. **로그아웃 URL** 텍스트 상자에 `https://impl.workday.com/<tenant>/login-saml.htmld` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 회신 URL 및 로그아웃 URL로 이러한 값을 업데이트합니다. 회신 URL에 하위 도메인(예: www, wd2, wd3, wd3-impl, wd5, wd5-impl)이 있어야 합니다.
    > `http://www.myworkday.com`과 같이 사용해도 되지만 `http://myworkday.com`은 가능하지 않습니다. 이러한 값을 얻으려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Workday 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 **nameidentifier** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다. Workday 애플리케이션에서는 **nameidentifier** 가 **user.mail**, **UPN** 등과 매핑되어야 하므로 **편집** 아이콘을 클릭하여 특성 매핑을 편집하여 특성 매핑을 변경해야 합니다.

    ![스크린샷은 편집 아이콘이 선택된 사용자 특성을 보여줍니다.](common/edit-attribute.png)

    > [!NOTE]
    > 여기에서는 기본적으로 UPN(user.userprincipalname)을 사용하여 이름 ID를 매핑했습니다. SSO를 성공적으로 수행하려면 Workday 계정(이메일, UPN 등)에서 이름 ID를 실제 사용자 ID와 매핑해야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

1. 요구 사항에 따라 **서명** 옵션을 수정하려면 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![인증서](common/edit-certificate.png) 

    ![SAML 서명 인증서](./media/workday-tutorial/signing-option.png)

    a. **서명 옵션** 에 대해 **SAML 응답 및 어설션 서명** 을 선택합니다.

    b. 페이지 맨 아래에 있는 **저장**

1. **Workable 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Workday에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Workday** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-workday"></a>Workday 구성

1. 다른 웹 브라우저 창에서 Workday 회사 사이트에 관리자 권한으로 로그인합니다.

1. **검색 상자** 에서 홈페이지 왼쪽 상단에 있는 이름 **테넌트 설정 편집 - 보안** 으로 검색합니다.

    ![테넌트 보안 편집](./media/workday-tutorial/search-box.png "테넌트 보안 편집")


1. **SAML 설정** 섹션에서 **ID 공급자 가져오기** 를 클릭합니다.

    ![SAML 설정](./media/workday-tutorial/saml-setup.png "SAML 설정")

1. **ID 공급자 가져오기** 섹션에서 다음 단계를 수행합니다.

    ![ID 공급자 가져오기](./media/workday-tutorial/import-identity-provider.png)

    a. 텍스트 상자에 `AzureAD`와 같은 **ID 공급자 이름** 을 지정합니다.

    b. **환경에 사용됨** 텍스트 상자의 드롭다운에서 적절한 환경 이름을 선택합니다.

    다. **파일 선택** 을 클릭하여 다운로드한 **페더레이션 메타데이터 XML** 파일을 업로드합니다.

    d. **확인** 을 클릭한 다음, **완료** 를 클릭합니다.

1. **완료** 를 클릭하면 새 행이 **SAML ID 공급자** 에 추가된 다음, 새로 만든 행에 대해 아래 단계를 추가할 수 있습니다.

    ![SAML ID 공급자.](./media/workday-tutorial/saml-identity-providers.png "SAML ID 공급자")

    a. **IDP 시작 로그아웃 사용** 확인란을 클릭합니다.

    b. **로그아웃 응답 URL** 텍스트 상자에 **http://www.workday.com** 를 입력합니다.

    다. **Workday 시작 로그아웃 사용** 확인란을 클릭합니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 요청 URL** 텍스트 상자에 붙여넣습니다.

    e. **SP 시작** 확인란을 클릭합니다.

    f. **서비스 공급자 ID** 텍스트 상자에 **http://www.workday.com** 을 입력합니다.

    **SP 시작 인증 요청을 Deflate하지 않음** 을 선택합니다.

1. 아래 이미지에서 다음 단계를 수행합니다.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML ID 공급자")

    a. **서비스 공급자 ID(사용 중단 예정)** 텍스트 상자에 **http://www.workday.com** 을 입력합니다.

    b. **IDP SSO 서비스 URL(사용 중단 예정)** 텍스트 상자에 **로그인 URL** 값을 입력 합니다.

    다. **SP에서 시작된 인증 요청을 Deflate하지 않음(사용 중단 예정)** 을 선택합니다.

    d. **인증 요청 서명 방법** 에서 **SHA256** 을 선택합니다.

    e. **확인** 을 클릭합니다.

    > [!NOTE]
    > Single Sign-On을 올바르게 설정했는지 확인하세요. 잘못된 설정으로 SSO(Single Sign-On)를 사용하도록 설정한 경우 자격 증명을 애플리케이션에 입력하지 못하고 잠길 수 있습니다. 이 상황에서 Workday는 사용자가 다음 형식의 일반 사용자 이름과 암호를 사용하여 로그인할 수 있는 백업 로그인 URL을 제공합니다.[Your Workday URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Workday 테스트 사용자 만들기

1. Workday 회사 사이트에 관리자 권한으로 로그인합니다.

1. 오른쪽 상단 모서리에 있는 **프로필** 을 클릭하고 **애플리케이션** 탭에서 **홈** 을 선택하고 **디렉터리** 를 클릭합니다. 

1. **디렉터리** 페이지에서 보기 탭의 **작업자 찾기** 를 선택합니다.

    ![작업자 찾기](./media/workday-tutorial/user-directory.png)

1.  **작업자 찾기** 페이지의 결과에서 사용자를 선택합니다.

1. 다음 페이지에서 **작업 > 작업자 보안** 을 선택하고 **Workday 계정** 이 **이름 ID** 값으로 Azure 활성 디렉터리와 일치해야 합니다.

    ![작업자 보안](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Workday 테스트 사용자를 만드는 방법에 대한 자세한 내용은 [Workday 클라이언트 지원팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Workday 로그온 URL로 리디렉션됩니다. 

* Workday 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Workday 타일을 클릭하면 SSO를 설정한 Workday에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Workday를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
