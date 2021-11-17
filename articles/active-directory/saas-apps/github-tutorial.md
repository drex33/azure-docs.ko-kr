---
title: '자습서: GitHub Enterprise Cloud Organization과 Azure AD SSO 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub Enterprise Cloud Organization 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: e6a56077adc6456d5d1ea6d5180a21560e193bb8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285743"
---
# <a name="tutorial-azure-ad-sso-integration-with-a-github-enterprise-cloud-organization"></a>자습서: GitHub Enterprise Cloud Organization과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GitHub Enterprise Cloud **Organization** 을 통합하는 방법에 대해 알아봅니다. GitHub Enterprise Cloud Organization을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* GitHub Enterprise Cloud Organization에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
* Azure Portal 중앙에서 GitHub Enterprise Cloud Organization에 대한 액세스를 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)에서 만든 GitHub 조직. 여기에는 [GitHub Enterprise 청구 플랜](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)이 필요합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* GitHub에서 **SP** 시작 SSO를 지원합니다.

* GitHub에서 [**자동화된** 사용자 프로비저닝(조직 초대)](github-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-github-from-the-gallery"></a>갤러리에서 GitHub 추가

Azure AD에 GitHub 통합을 구성하려면 갤러리의 GitHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **GitHub** 를 입력합니다.
1. 결과 패널에서 **GitHub Enterprise Cloud Organization** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>GitHub에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 GitHub에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 GitHub의 관련 사용자 간에 연결 관계를 설정해야 합니다.

GitHub에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[GitHub SSO 구성](#configure-github-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[GitHub 테스트 사용자 만들기](#create-github-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 GitHub에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **GitHub** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://github.com/orgs/<Organization ID>` 패턴을 사용하는 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://github.com/orgs/<Organization ID>/saml/consume` 패턴을 사용하여 URL을 입력합니다.

    다. **로그온 URL** 텍스트 상자에서 `https://github.com/orgs/<Organization ID>/sso` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL로 이러한 값을 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. GitHub 관리자 섹션으로 이동하여 이러한 값을 검색합니다.

5. GitHub 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 GitHub 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 **고유 사용자 식별자(이름 ID)** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다. GitHub 애플리케이션에서는 **고유 사용자 식별자(이름 ID)** 가 **user.mail** 과 매핑되므로 특성 매핑을 변경하려면 **편집** 아이콘을 클릭하고 특성 매핑을 편집해야 합니다.

    !["편집" 아이콘이 선택된 "사용자 특성" 섹션을 보여주는 스크린샷.](common/edit-attribute.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **GitHub 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 GitHub에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **GitHub** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.

    ![사용자 역할](./media/github-tutorial/user-role.png)

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-github-sso"></a>GitHub SSO 구성

1. 다른 웹 브라우저 창에서 GitHub 조직 사이트에 관리자로 로그인합니다.

2. **설정** 으로 이동하여 **보안** 을 클릭합니다.

    !["보안"이 선택된 GitHub "조직 설정" 메뉴를 보여주는 스크린샷.](./media/github-tutorial/security.png)

3. Single Sign-On 구성 필드가 표시된 **SAML 인증 사용** 상자를 선택하여 다음 단계를 수행합니다.

    ![URL 텍스트 상자가 강조 표시된 "SAML 인증 사용"이 있는 "SAML Single Sign-On" 섹션을 보여주는 스크린샷.](./media/github-tutorial/authentication.png)

    a. **Single Sign-On URL** 값을 복사하고 이 값을 Azure Portal의 **기본 SAML 구성** 에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.
    
    b. **Assertion Consumer Service URL** 값을 복사하고 이 값을 Azure Portal의 **기본 SAML 구성** 에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

4. 다음 필드를 구성합니다.

    !["로그온 URL", "발급자" 및 "공용 인증서" 텍스트 상자를 보여주는 스크린샷.](./media/github-tutorial/configure.png)

    a. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서 파일의 내용을 메모장에 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    d. 아래와 같이 **편집** 아이콘을 클릭하여 **RSA-SHA1** 및 **SHA1** 의 **서명 방법** 및 **다이제스트 메서드** 를 **RSA-SHA256** 및 **SHA256** 으로 편집합니다.
    
    e. GitHub의 URL이 Azure 앱 등록의 URL과 일치하도록 기본 URL에서 **Assertion Consumer Service URL(회신 URL)** 을 업데이트합니다.

    ![이미지를 보여주는 스크린샷](./media/github-tutorial/certificate.png)

5. **SAML 구성 테스트** 를 클릭하여 SSO 동안 유효성 검사 실패 또는 오류가 없는지 확인합니다.

    ![설정을 보여주는 스크린샷](./media/github-tutorial/test.png)

6. 페이지 맨 아래에 있는 **저장**

> [!NOTE]
> GitHub의 Single Sign-On은 GitHub의 특정 조직에 인증되며 GitHub 자체의 인증을 대체하지 않습니다. 따라서 사용자의 GitHub.com 세션이 만료되면 Single Sign-On 프로세스 중에 GitHub의 ID/암호로 인증하라는 메시지가 표시될 수 있습니다.

### <a name="create-github-test-user"></a>GitHub 테스트 사용자 만들기

이 섹션은 GitHub에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. GitHub는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](github-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. GitHub 회사 사이트에 관리자 권한으로 로그인합니다.

2. **피플** 을 클릭합니다.

    ![스크린샷은 사용자가 선택한 GitHub 사이트를 보여줍니다.](./media/github-tutorial/people.png "사람")

3. **멤버 초대** 를 클릭합니다.

    ![사용자 초대를 보여주는 스크린샷](./media/github-tutorial/invite-member.png "사용자 초대")

4. **멤버 초대** 대화 상자 페이지에서 다음 단계를 수행합니다.

    a. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![사람 초대를 보여주는 스크린샷](./media/github-tutorial/email-box.png "피플 초대")

    b. **초대 보내기** 를 클릭합니다.

    !["멤버"가 선택되고 "초대 보내기" 단추가 선택된 "멤버 초대" 대화 상자 페이지를 보여주는 스크린샷.](./media/github-tutorial/send-invitation.png "피플 초대")

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 GitHub 로그온 URL로 리디렉션됩니다. 

* GitHub 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 GitHub 타일을 클릭하면 GitHub 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

GitHub가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
